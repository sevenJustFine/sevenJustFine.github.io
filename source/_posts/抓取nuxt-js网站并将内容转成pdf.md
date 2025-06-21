---
title: 抓取nuxt.js网站并将内容转成pdf
date: 2025-06-21 09:56:15
tags:
---



### 1.直接使用python+playwright获取渲染之后html源代码 
 避免需要去处理js,使用flask搭建一个接口,传入url 获取源代码
 
```python
from flask import Flask, request
from playwright.sync_api import sync_playwright

@app.route('/html/url', methods=['POST'])
def get_html():
    url = request.form.get('url')
    html = get_html_by_url(url)
    return html

def get_html_by_url(url):
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=True)
        page = browser.new_page()
        page.goto(url, wait_until="networkidle")
        html = page.content()
        browser.close()
        return html
if __name__ == '__main__':
    app.run(debug=False, use_reloader=False)
```

### 2.将获取的源代码 转成pdf

#### 2.1 下载支持汉字的字体文件 
下载字体是为了避免出现乱码
https://fonts.google.com/?lang=zh_Hans  
从下载的字体包中选择一个上传到本地,我选择了
NotoSerifSC-Regular.ttf


#### 2.2 php安装扩展 
```shell
composer require barryvdh/laravel-dompdf
```

```php
    use Barryvdh\DomPDF\Facade\Pdf;
    use QL\QueryList;

    public static function html2PDF($data)
    {
        $html = $data->content;
        //处理懒加载的图片
        $ql = QueryList::html($html);
        $ql->find('img')->each(function ($item) {
            $data_src = $item->attr('data-src');
            $src = $item->attr('src');
            //去除?后的内容
            $data_src = substr($data_src, 0, strrpos($data_src, '?'));
            $src = substr($src, 0, strrpos($src, '?'));

            if ($src != $data_src) {
                $item->attr('src', $data_src);
            }
        });
        $html = $ql->getHtml();
        $html = str_replace('&nbsp;', ' ', $html); // 替换 HTML 中的&nbsp;
        $html = preg_replace('/\x{00A0}/u', ' ', $html); // 替换 Unicode 不可见空格

        //指定字体避免乱码
        // 加字体样式
        $style = '
    <style>
        @font-face {
            font-family: "Noto_Serif_SC";
            src: url("' . storage_path('fonts/NotoSerifSC-Regular.ttf') . '");
        }
        body {
            font-family: "Noto_Serif_SC", DejaVu Sans, sans-serif;
        }
        p { line-height: 1.5; font-size: 14px; }
    </style>
';

        $html = $style . $html;

        $pdf = Pdf::loadHTML($html, 'UTF-8');

        $cate_id = $data->cate_id;
        $cate = Db::table('cate')->where('id', $cate_id)->first();
        $cate_name = $cate->name;
        $relativePath = 'pdf/' . $cate_name . '/' . str_replace('姓名：', '', $data->title) . '.pdf';

        // 确保目录存在
        Storage::disk('public')->makeDirectory('pdf');
        // 保存 PDF 文件到指定路径
        $re = Storage::disk('public')->put($relativePath, $pdf->output());
        dump("$cate_name,$data->title,$re");

    }


```