## 新增博文
```bash
hugo new content content/post/${articleName_en}/index.md
```



## Hugo 0.146.0版本报错解决方案:
```bash
partial "partials/templates/_funcs/get-page-images" not found
```

这是因为 Hugo 0.146.0 之后改变了 partial 模板查找的行为，不再进行双重查找。

### 方法 1: 创建缺失的模板文件

```bash
mkdir -p themes/PaperMod/layouts/partials/templates/_funcs
touch themes/PaperMod/layouts/partials/templates/_funcs/get-page-images.html
```

### 方法 2: 修改 twitter_cards.html 文件

找到并编辑 twitter_cards.html 文件:

```bash
vim themes/PaperMod/layouts/partials/templates/twitter_cards.html
```

将第 9 行左右的 `partial "partials/templates/_funcs/get-page-images"` 修改为 `partial "templates/_funcs/get-page-images"`。

### 方法 3: 更新 PaperMod 主题

```bash
cd themes/PaperMod
git pull
cd ../..
```

### 方法 4: 在配置中忽略警告 (仅抑制警告，不解决错误)

在 `config.yaml` 或 `config.toml` 中添加:

```yaml
ignoreLogs = ['warning-partial-superfluous-prefix']
```

推荐使用方法 2 或方法 3，因为这些是真正解决问题的方法。

Arcolaus: @terminal /explain #terminalSelection

Terminal: # 解决 Hugo PaperMod 模板错误问题

看起来您遇到了 Hugo 中的模板路径解析问题。错误显示找不到 `partial "partials/templates/_funcs/get-page-images"`，但从您的代码可以看到实际使用的是 `partial "templates/_funcs/get-page-images"`。

## 解决方案：

### 方法1：编辑 schema_json.html 文件
根据错误信息，问题出在 schema_json.html 文件中：

```bash
vim themes/PaperMod/layouts/partials/templates/schema_json.html
```

找到包含 `partial "partials/templates/_funcs/get-page-images"` 的行（大约第88行），将其修改为：

```html
{{- $images := partial "templates/_funcs/get-page-images" . -}}
```

### 方法2：创建缺失的模板文件

```bash
mkdir -p themes/PaperMod/layouts/partials/partials/templates/_funcs
cp themes/PaperMod/layouts/partials/templates/_funcs/get-page-images.html themes/PaperMod/layouts/partials/partials/templates/_funcs/
```

### 方法3：更新 PaperMod 主题

```bash
cd themes/PaperMod
git pull
cd ../..
```

### 方法4：在配置中忽略相关警告

在您的 config.toml 或 config.yaml 中添加：

```yaml
ignoreErrors = ["error-remote-getjson", "error-missing-instagram-accesstoken"]
```

推荐使用方法1或方法3，这些是最彻底的解决方案。

