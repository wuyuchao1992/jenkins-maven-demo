SampleResult result = WDS.sampleResult
result.setStampAndTime(0, 0)

WDS.browser.get("https://你的页面")

result.sampleStart()

// 步骤1
result.subSampleStart("步骤1")
WDS.browser.findElement(org.openqa.selenium.By.id("step1")).click()
result.subSampleEnd(true)

// 步骤2
result.subSampleStart("步骤2")
WDS.browser.findElement(org.openqa.selenium.By.id("step2")).click()
result.subSampleEnd(true)

// 步骤3
result.subSampleStart("步骤3")
WDS.browser.findElement(org.openqa.selenium.By.id("step3")).click()
result.subSampleEnd(true)

// 步骤4
result.subSampleStart("步骤4")
WDS.browser.findElement(org.openqa.selenium.By.id("step4")).click()
result.subSampleEnd(true)

// 步骤5
result.subSampleStart("步骤5")
WDS.browser.findElement(org.openqa.selenium.By.id("step5")).click()
result.subSampleEnd(true)

// 步骤6
result.subSampleStart("步骤6")
WDS.browser.findElement(org.openqa.selenium.By.id("step6")).click()
result.subSampleEnd(true)

result.sampleEnd()
result.setSuccessful(true)