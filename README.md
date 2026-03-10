// 只保留这一段，其它都删掉
WDS.sampleResult.clear();
WDS.sampleResult.setSampleLabel("你的步骤名");

WDS.sampleResult.sampleStart();

// ========== 你的 WebDriver 代码 ==========
// WDS.browser.get(...)
// WDS.findElement(...)
// ========================================

WDS.sampleResult.sampleEnd();