
// 关键：禁止 WebDriver Sampler 自动生成结果
ctx.setSampleStarted(false)

// 自己创建样本
SampleResult res = new SampleResult()
res.setSampleLabel("你的业务步骤名")
res.sampleStart()

// ====================
// 这里放你的 WebDriver 代码
// WDS.browser.get("xxx")
// WDS.findElement...
// ====================

res.sampleEnd()

// 标记成功/失败
res.setSuccessful(true)
res.setResponseCode("200")
res.setResponseMessage("OK")

// 把结果交给 JMeter
sampler.setSampleResult(res)