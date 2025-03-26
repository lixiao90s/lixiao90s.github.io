---
title: 思源笔记转换Jekyll博客记录
date: 2025-03-12 11:45:00 +0800
categories:
- 技术笔记
tags:
- python
- jekyll
- markdown
author: lixiao
layout: post
toc: true
---

* TOC
{:toc}

# 思源笔记转换Jekyll博客记录

## 一、项目背景

将思源笔记中的 Markdown 文档转换为 Jekyll 博客文章，主要需要处理以下问题：

1. 提取文章标题和分类
2. 生成合适的文件名
3. 处理图片路径
4. 生成文章目录
5. 添加合适的标签
6. 处理 front matter

## 二、转换脚本实现

### 2.1 主要功能

1. 自动检测文章分类和标签
2. 处理图片路径，支持本地图片和外部链接
3. 生成文章目录
4. 添加必要的 front matter
5. 处理文件名和日期

### 2.2 核心代码

```python
#!/usr/bin/env python3
import os
import re
import shutil
from datetime import datetime
import yaml
import hashlib
import logging

# 设置日志
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

def sanitize_filename(title):
    """将中文标题转换为合法的文件名"""
    # 移除特殊字符，用连字符替换空格
    filename = re.sub(r'[^\w\s-]', '', title)
    filename = re.sub(r'[-\s]+', '-', filename).strip('-')
    return filename.lower()

def detect_categories_and_tags(content):
    """从内容中检测可能的分类和标签"""
    categories = []
    tags = []
    
    # 从一级标题检测分类
    category_match = re.search(r'^#\s+(.+?)(?:\n|$)', content, re.MULTILINE)
    if category_match:
        categories.append(category_match.group(1).strip())
    
    # 检测技术关键词作为标签
    tech_keywords = ['spring', 'java', 'mybatis', 'maven', 'framework', 'boot', 'mvc', 
                    'python', 'javascript', 'typescript', 'react', 'vue', 'angular',
                    'docker', 'kubernetes', 'linux', 'git', 'database', 'sql']
    for keyword in tech_keywords:
        if keyword.lower() in content.lower():
            tags.append(keyword)
    
    return list(set(categories)), list(set(tags))

def process_markdown_content(content, assets_dir):
    """处理 Markdown 内容"""
    # 在文章开头添加目录
    content = "* TOC\n{:toc}\n\n" + content
    
    # 处理思源图片路径
    def replace_image(match):
        image_url = match.group(2)
        if image_url.startswith('http'):
            return match.group(0)  # 保持外部链接不变
        
        # 为本地图片生成唯一文件名
        image_name = hashlib.md5(image_url.encode()).hexdigest()[:8]
        if '.' in image_url:
            ext = image_url.rsplit('.', 1)[1].lower()
            if ext not in ['jpg', 'jpeg', 'png', 'gif', 'svg']:
                ext = 'png'
            new_name = f"{image_name}.{ext}"
        else:
            new_name = f"{image_name}.png"
        
        return f"![{match.group(1)}](/assets/images/{new_name})"
    
    content = re.sub(r'!\[(.*?)\]\((.*?)\)', replace_image, content)
    
    # 处理内部链接
    content = re.sub(r'\[\[(.*?)\]\]', r'\1', content)
    
    # 移除多余的空行
    content = re.sub(r'\n{3,}', '\n\n', content)
    
    return content

def create_front_matter(title, date, categories=None, tags=None):
    """创建 Jekyll front matter"""
    front_matter = {
        'title': title,
        'date': date.strftime('%Y-%m-%d %H:%M:%S +0800'),
        'categories': categories or ['未分类'],
        'tags': tags or [],
        'author': 'lixiao',
        'layout': 'post',
        'toc': True
    }
    return f"---\n{yaml.dump(front_matter, allow_unicode=True, sort_keys=False)}---\n\n"

def convert_siyuan_to_jekyll(siyuan_dir, jekyll_dir):
    """转换思源笔记到 Jekyll 博客格式"""
    try:
        # 确保路径是绝对路径
        siyuan_dir = os.path.abspath(siyuan_dir)
        jekyll_dir = os.path.abspath(jekyll_dir)
        
        if not os.path.exists(siyuan_dir):
            logging.error(f"思源笔记目录不存在: {siyuan_dir}")
            return
        
        # 创建必要的目录
        posts_dir = os.path.join(jekyll_dir, '_posts')
        assets_dir = os.path.join(jekyll_dir, 'assets', 'images')
        os.makedirs(posts_dir, exist_ok=True)
        os.makedirs(assets_dir, exist_ok=True)
        
        logging.info(f"开始转换，源目录: {siyuan_dir}")
        logging.info(f"目标目录: {jekyll_dir}")

        # 遍历思源笔记目录
        for root, dirs, files in os.walk(siyuan_dir):
            for file in files:
                if file.endswith('.md'):
                    try:
                        # 读取原始文件
                        file_path = os.path.join(root, file)
                        with open(file_path, 'r', encoding='utf-8') as f:
                            content = f.read()

                        # 提取标题
                        title_match = re.search(r'^#\s+(.+?)(?:\n|$)', content)
                        if title_match:
                            title = title_match.group(1).strip()
                        else:
                            title = os.path.splitext(file)[0]
                        
                        # 检测分类和标签
                        categories, tags = detect_categories_and_tags(content)
                        
                        # 使用文件修改时间作为文章日期
                        date = datetime.fromtimestamp(os.path.getmtime(file_path))
                        
                        # 生成文件名
                        filename = f"{date.strftime('%Y-%m-%d')}-{sanitize_filename(title)}.md"
                        
                        # 处理内容
                        content = process_markdown_content(content, assets_dir)
                        
                        # 添加 front matter
                        content = create_front_matter(title, date, categories, tags) + content
                        
                        # 保存新文件
                        new_file_path = os.path.join(posts_dir, filename)
                        with open(new_file_path, 'w', encoding='utf-8') as f:
                            f.write(content)

                        logging.info(f"已转换: {filename}")
                    except Exception as e:
                        logging.error(f"处理文件 {file} 时出错: {str(e)}")

        # 复制资源文件
        assets_src = os.path.join(siyuan_dir, 'assets')
        if os.path.exists(assets_src):
            for root, dirs, files in os.walk(assets_src):
                for file in files:
                    if file.lower().endswith(('.png', '.jpg', '.jpeg', '.gif', '.svg')):
                        try:
                            src_path = os.path.join(root, file)
                            image_name = hashlib.md5(file.encode()).hexdigest()[:8]
                            ext = file.rsplit('.', 1)[1].lower()
                            new_name = f"{image_name}.{ext}"
                            dst_path = os.path.join(assets_dir, new_name)
                            shutil.copy2(src_path, dst_path)
                            logging.info(f"已复制资源: {new_name}")
                        except Exception as e:
                            logging.error(f"复制资源文件 {file} 时出错: {str(e)}")
    except Exception as e:
        logging.error(f"转换过程中出错: {str(e)}")

if __name__ == '__main__':
    import sys
    if len(sys.argv) != 3:
        print("用法: python convert_siyuan.py <思源笔记目录> <Jekyll博客目录>")
        sys.exit(1)
    
    siyuan_dir = sys.argv[1]
    jekyll_dir = sys.argv[2]
    convert_siyuan_to_jekyll(siyuan_dir, jekyll_dir)
```

## 三、主要功能说明

### 3.1 文件名处理

使用 `sanitize_filename` 函数将中文标题转换为合法的文件名：
1. 移除特殊字符
2. 用连字符替换空格
3. 转换为小写

### 3.2 分类和标签检测

使用 `detect_categories_and_tags` 函数自动检测文章的分类和标签：
1. 从一级标题提取分类
2. 从内容中检测技术关键词作为标签
3. 去重处理

### 3.3 内容处理

使用 `process_markdown_content` 函数处理 Markdown 内容：
1. 添加文章目录标记
2. 处理图片路径
3. 处理内部链接
4. 清理多余空行

### 3.4 Front Matter 生成

使用 `create_front_matter` 函数生成 Jekyll 所需的 front matter：
1. 设置标题、日期、分类和标签
2. 添加作者和布局信息
3. 启用目录功能

### 3.5 图片处理

1. 支持外部链接图片
2. 本地图片生成唯一文件名
3. 自动复制图片文件到博客目录

## 四、使用方法

1. 将脚本保存为 `convert_siyuan.py`
2. 在命令行中运行：

```bash
python convert_siyuan.py "思源笔记目录" "Jekyll博客目录"
```

例如：

```bash
python tools/convert_siyuan.py "E:/siyuanmd" "E:/lixiao90s.github.io"
```

## 五、注意事项

1. 确保思源笔记目录存在
2. 确保 Jekyll 博客目录有写入权限
3. 图片文件需要在 assets 目录下
4. 文章日期使用文件修改时间
5. 标签根据预定义的技术关键词自动生成

## 六、后续优化方向

1. 支持更多图片格式
2. 添加更多技术关键词
3. 支持自定义标签规则
4. 优化目录生成
5. 添加更多错误处理
6. 支持批量转换和增量转换 