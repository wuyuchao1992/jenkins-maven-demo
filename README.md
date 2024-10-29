import { Page } from 'playwright';
import { FamilyFieldList } from './types'; // 假设类型定义在types文件中

export class MyPage {
  private page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  // 根据name字段定位并返回对应cell的内容，同时检查是否带有*
  async checkFieldsAndAsterisk(familyFieldList: FamilyFieldList): Promise<void> {
    for (const field of familyFieldList.fields) {
      const cellLocator = this.page.locator(`text=${field.name}`);
      
      // 检查元素是否存在
      const cellExists = await cellLocator.count() > 0;
      if (!cellExists) {
        console.log(`Cell with field name "${field.name}" not found.`);
        continue;
      }

      // 获取单元格内容
      const cellContent = await cellLocator.textContent();

      if (!field.nullable) {
        // nullable为false时，检查是否包含'*'
        if (cellContent?.includes('*')) {
          console.log(`Cell with field name "${field.name}" contains "*".`);
        } else {
          console.log(`Cell with field name "${field.name}" does not contain "*", but it is required.`);
        }
      } else {
        console.log(`Field "${field.name}" is nullable, skipping * check.`);
      }
    }
  }
}