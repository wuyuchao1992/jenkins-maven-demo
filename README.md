import { Page } from 'playwright';
import { FamilyFieldList } from './types'; // 假设类型定义在types文件中

export class MyPage {
  private page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  // 方法：根据name字段获取对应cell的内容
  async getCellContentByName(fieldName: string): Promise<string | null> {
    const cellLocator = this.page.locator(`text=${fieldName}`);

    // 检查元素是否存在
    const cellExists = await cellLocator.count() > 0;
    if (!cellExists) {
      console.log(`Cell with field name "${fieldName}" not found.`);
      return null;
    }

    // 返回单元格内容
    return await cellLocator.textContent();
  }

  // 方法：判断内容是否包含'*'
  async isAsteriskRequired(cellContent: string | null): Promise<boolean> {
    if (cellContent && cellContent.includes('*')) {
      return true;
    }
    return false;
  }

  // 遍历familyFieldList，进行校验
  async checkFieldsAndAsterisk(familyFieldList: FamilyFieldList): Promise<void> {
    for (const field of familyFieldList.fields) {
      const cellContent = await this.getCellContentByName(field.name);

      if (!field.nullable) {
        // nullable为false时，检查是否包含'*'
        const containsAsterisk = await this.isAsteriskRequired(cellContent);

        if (containsAsterisk) {
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