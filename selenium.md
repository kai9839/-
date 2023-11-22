### 1、selenium安装和使用

[selenium入门超详细教程——网页自动化操作-CSDN博客](https://blog.csdn.net/kobepaul123/article/details/128796839)

**注意：** selenium启动浏览器，运行完代码，会自动退出浏览器
**解决方法：** 

[Selenium安装WebDriver最新Chrome驱动(含116/117/118/119)_chromedriver-CSDN博客](https://blog.csdn.net/Z_Lisa/article/details/133307151)
1、降低selenium版本，使用4.1.1 / 4.5.0 版本

```
pip --default-timeout=100 install selenium==4.1.1 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

2、发现登录后还是闪退，但是版本号都是对应的，可能是因为driver的[全局变量](https://so.csdn.net/so/search?q=全局变量&spm=1001.2101.3001.7020)问题导致的
	2.1 把driver放在函数外，为全局不会闪退

```
from selenium import webdriver
 
chromedriver_path = r"C:\Users\AppData\Local\Google\Chrome\Application\chromedriver.exe"
driver = webdriver.Chrome(chromedriver_path)
 
 
# 登录百度
def main():
    driver.get("https://baidu.com/")
 
 
if __name__ == '__main__':
    main()
```

​	2.2 不设置driver为全局，放在函数内会闪退

```
from selenium import webdriver
 
 
# 登录百度
def main():
    chromedriver_path =r"C:\Users\AppData\Local\Google\Chrome\Application\chromedriver.exe"
    driver = webdriver.Chrome(chromedriver_path)
    driver.get("https://baidu.com/")
 
 
if __name__ == '__main__':
    main()
```

​	2.3 也可以把driver放在函数内，只要设置为全局变量就可以

```
from selenium import webdriver
 
 
# 登录百度
def main():
    global driver     # 设置全局变量
    chromedriver_path =r"C:\Users\AppData\Local\Google\Chrome\Application\chromedriver.exe"
    driver = webdriver.Chrome(chromedriver_path)
    driver.get("https://baidu.com/")
 
 
if __name__ == '__main__':
    main()
```

### 2、使用XPath和Class定位不到元素

1. **使用其他属性**：除了常见的定位方式外，还可以尝试使用元素的其他属性来进行定位，例如id、name、tag name等。

2. **使用CSS选择器**：有时候使用CSS选择器可以更灵活地定位元素，语法简洁清晰。例如，`driver.find_element_by_css_selector("input#username")`。

3. **使用父子关系**：如果某个元素无法直接定位，可以尝试通过其父元素或者祖先元素来间接定位。

4. **显式等待**：有时候页面的元素需要一定时间才会加载出来，可以使用显式等待的方式等待元素出现。

5. **查看页面源代码**：可以查看页面源代码，确认元素的确存在于页面中，以及元素的实际属性值是否符合预期。

   ```
   from selenium.webdriver.common.by import By
   from selenium.webdriver.support.ui import WebDriverWait
   from selenium.webdriver.support import expected_conditions as EC
   
   # 使用CSS选择器来定位
   element = driver.find_element(By.CSS_SELECTOR, "button.btn-primary")
   
   # 显式等待
   element = WebDriverWait(driver, 10).until(
       EC.presence_of_element_located((By.CSS_SELECTOR, "button.btn-primary"))
   )
   
   ```

   

   在Selenium中，使用XPath定位元素可以通过以下步骤实现：

   1. 首先，需要导入相应的模块，包括`By`和`WebDriverWait`，以及`expected_conditions`。

   ```python
   from selenium.webdriver.common.by import By
   from selenium.webdriver.support.ui import WebDriverWait
   from selenium.webdriver.support import expected_conditions as EC
   ```

   2. 然后，通过`find_element()`或者`find_elements()`方法来查找目标元素。其中，`find_element()`用于查找单个元素，而`find_elements()`用于查找多个元素。这两个方法都接受两个参数：定位方法和定位表达式。

   ```python
   driver.find_element(By.XPATH, "//input[@id='username']")
   driver.find_elements(By.XPATH, "//a[@class='nav-link']")
   ```

   3. 如果需要等待元素出现再进行操作，可以使用`WebDriverWait`和`expected_conditions`来等待目标元素的出现。例如，使用`presence_of_element_located()`方法等待元素出现：

   ```python
   wait = WebDriverWait(driver, 10)
   element = wait.until(EC.presence_of_element_located((By.XPATH, "//input[@id='username']")))
   ```

   这样就可以在Selenium中使用XPath来定位元素了。XPath的表达式可以根据具体的页面结构和元素属性来自由组合，并且支持多种查询方式，非常灵活和强大。如果你需要进一步的帮助，欢迎随时提问。
