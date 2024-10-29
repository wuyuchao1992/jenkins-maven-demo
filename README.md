import { Page } from 'playwright';

export class MyPage {
  private page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  // 根据fieldName定位并返回对应cell的内容
  async getCellContentByField(fieldName: string): Promise<string | null> {
    const cellLocator = this.page.locator(`text=${fieldName}`);

    // 检查元素是否存在
    const cellExists = await cellLocator.count() > 0;
    if (!cellExists) {
      console.log(`Cell with field name "${fieldName}" not found.`);
      return null;
    }

    // 返回对应的cell内容
    const cellContent = await cellLocator.textContent();
    return cellContent;
  }
}