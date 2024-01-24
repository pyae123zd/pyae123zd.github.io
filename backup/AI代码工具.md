---
date created: 2024-01-11

---

### 可选工具

- [[Copilot](https://github.com/features/copilot)](https://github.com/features/copilot) 
  - $10 per month,$100 per year
- [[Codeium](https://codeium.com/)](https://codeium.com/)
  - free
- CodeGeeX(清华)
  - free
  - 直连
  - 可自定义训练
- 通义灵码(阿里)
  - free
  - 直连
- Tabline
- Replit
- ...

---

### 功能

- 生成代码
- 解释代码
- 优化代码
- 生成测试方法
- 生成注释
- 修复问题代码

### 代码生成工具使用

- 简单场景：
  - 联系上下文直接补全
- 有固定实现的功能：
  - 注释描述（最好是英文）
  - 直接使用方法名来提示
- 复杂场景
  - 注释描述
  - 拆分任务

## 效果对比

### 案例一：使用Java 写一个工具类 根据某个实体类的字段名 实现word中模版内容的填充

- Github Copilot

```java
import org.apache.poi.xwpf.usermodel.*;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.lang.reflect.Field;

public class WordFiller {
    public static void fillTemplate(Object entity, String templatePath, String outputPath) throws Exception {
        XWPFDocument doc = new XWPFDocument(new FileInputStream(new File(templatePath)));
        for (Field field : entity.getClass().getDeclaredFields()) {
            field.setAccessible(true);
            String name = field.getName();
            Object value = field.get(entity);
            for (XWPFParagraph p : doc.getParagraphs()) {
                for (XWPFRun r : p.getRuns()) {
                    String text = r.getText(0);
                    if (text != null && text.contains("{" + name + "}")) {
                        text = text.replace("{" + name + "}", value.toString());
                        r.setText(text, 0);
                    }
                }
            }
        }
        FileOutputStream out = new FileOutputStream(outputPath);
        doc.write(out);
        out.close();
    }
}
```

- Codeium

```java
import java.util.Map;

public class WordTemplateUtil {
    public static String fillTemplate(String template, Map<String, String> fieldMap) {
        for (Map.Entry<String, String> entry : fieldMap.entrySet()) {
            String fieldName = entry.getKey();
            String value = entry.getValue();
            template = template.replace("{{" + fieldName + "}}", value);
        }
        return template;
    }
}
```

提示输入为实体类后

```java
import java.lang.reflect.Field;
import java.util.Map;

public class WordTemplateUtil {
    public static String fillTemplateFromEntity(String template, YourEntityClass entity) throws IllegalAccessException {
        Class<?> entityClass = entity.getClass();
        Field[] fields = entityClass.getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            String fieldName = field.getName();
            String value = field.get(entity).toString();
            template = template.replace("{{" + fieldName + "}}", value);
        }
        return template;
    }
}
```

- 通义灵码

```java
import org.apache.poi.xwpf.usermodel.*;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.lang.reflect.Field;
import java.util.List;

public class WordTemplateFiller {

    public static void fillTemplate(MyEntity entity, String templatePath, String outputPath) throws Exception {
        // 读取Word模板文件
        FileInputStream fis = new FileInputStream(templatePath);
        XWPFDocument document = new XWPFDocument(fis);

        // 遍历实体类的所有字段
        Field[] fields = MyEntity.class.getDeclaredFields();
        for (Field field : fields) {
            // 设置访问权限为可访问
            field.setAccessible(true);

            // 获取字段名
            String fieldName = field.getName();

            // 在Word文档中查找与字段名匹配的占位符（例如 {{fieldName}}）
            List<XWPFParagraph> paragraphs = document.getParagraphs();
            for (XWPFParagraph paragraph : paragraphs) {
                List<XWPFRun> runs = paragraph.getRuns();
                for (XWPFRun run : runs) {
                    String text = run.getText(0);
                    if (text != null && text.contains("{{" + fieldName + "}}")) {
                        // 替换占位符为字段的实际值
                        Object fieldValue = field.get(entity);
                        String newValue = fieldValue != null ? fieldValue.toString() : "";
                        run.setText(newValue, 0);
                    }
                }
            }
        }

        // 写入填充后的内容到新的Word文件
        FileOutputStream out = new FileOutputStream(outputPath);
        document.write(out);
        out.close();
        document.close();
        fis.close();
    }
}

// 假设的实体类
class MyEntity {
    private String name;
    private String address;
    // 其他字段及对应的getter/setter...
}

```

- CodeGeeX

```python
from docx import Document

class EntityClass:
    def __init__(self, field1, field2, field3):
        self.field1 = field1
        self.field2 = field2
        self.field3 = field3

def fill_template(entity, template_path):
    # 创建一个新的Word文档
    doc = Document()

    # 打开模板文件
    with open(template_path, 'rb') as file:
        # 将模板内容添加到新的Word文档中
        doc.add_paragraph(file.read().decode('utf-8'))

    # 根据实体类的字段名进行模板内容的填充
    for paragraph in doc.paragraphs:
        for run in paragraph.runs:
            for key, value in entity.__dict__.items():
                text = f'{{{key}}}'
                if text in run.text:
                    run.text = run.text.replace(text, str(value))

    return doc

# 示例
entity = EntityClass('John Doe', 'Software Engineer', 30)
template_path = 'template.docx'
doc = fill_template(entity, template_path)
doc.save('output.docx')

```

生成了python代码

- ChatGpt

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Map;

import org.apache.poi.xwpf.usermodel.XWPFDocument;
import org.apache.poi.xwpf.usermodel.XWPFPicture;
import org.apache.poi.xwpf.usermodel.XWPFRun;
import org.apache.poi.xwpf.usermodel.XWPFSimpleShape;
import org.apache.poi.xwpf.usermodel.XWPFTable;
import org.apache.poi.xwpf.usermodel.XWPFTableRow;

public class WordTemplateFiller {

    public static void fillTemplate(String templatePath, String outputPath, Map<String, String> fieldValues) {
        try (FileInputStream fis = new FileInputStream(templatePath);
             XWPFDocument document = new XWPFDocument(fis)) {

            // 替换文本内容
            for (XWPFParagraph paragraph : document.getParagraphs()) {
                for (XWPFRun run : paragraph.getRuns()) {
                    String text = run.getText(0);
                    if (text != null) {
                        for (Map.Entry<String, String> entry : fieldValues.entrySet()) {
                            text = text.replace("${" + entry.getKey() + "}", entry.getValue());
                        }
                        run.setText(text, 0);
                    }
                }
            }

            // 替换表格内容
            for (XWPFTable table : document.getTables()) {
                for (XWPFTableRow row : table.getRows()) {
                    for (XWPFRun run : row.getTableCells().get(0).getParagraphs().get(0).getRuns()) {
                        String text = run.getText(0);
                        if (text != null) {
                            for (Map.Entry<String, String> entry : fieldValues.entrySet()) {
                                text = text.replace("${" + entry.getKey() + "}", entry.getValue());
                            }
                            run.setText(text, 0);
                        }
                    }
                }
            }

            try (FileOutputStream fos = new FileOutputStream(outputPath)) {
                document.write(fos);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        // 示例用法
        String templatePath = "path/to/your/template.docx";
        String outputPath = "path/to/your/output.docx";

        // 字段名和对应的值
        Map<String, String> fieldValues = Map.of(
                "FieldName1", "Value1",
                "FieldName2", "Value2",
                // 添加更多字段
        );

        fillTemplate(templatePath, outputPath, fieldValues);
    }
}

```

### 案例二：使用Java写个从prometheus中获取当前CPU占用率TOP5的主机的方法

- Github Copilot
  ![Pasted image 20240124103118](https://raw.githubusercontent.com/pyae123zd/PicoRepo/main/img/Pasted%20image%2020240124103118.png)

> `100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`：这个查询关注的是每个实例的CPU使用率。它返回的是每个实例的CPU使用率，这个值是一个百分比。这个查询更关注的是每个实例的CPU使用率。

- 通义灵码

![Pasted image 20240124102541](https://raw.githubusercontent.com/pyae123zd/PicoRepo/main/img/Pasted%20image%2020240124102541.png)

> `sort_desc(topk(5, avg(node_cpu_seconds_total{job='node'}[5m])) by (instance))`：这个查询关注的是过去5分钟内CPU使用时间最长的前5个实例。它返回的是一个实例列表，这些实例按照CPU使用时间从高到低排序。这个查询更关注的是哪些实例的CPU使用时间最长。  

- CodeGeex
  ![Pasted image 20240124102815](https://raw.githubusercontent.com/pyae123zd/PicoRepo/main/img/Pasted%20image%2020240124102815.png)
- Codeium
  ![Pasted image 20240124102939](https://raw.githubusercontent.com/pyae123zd/PicoRepo/main/img/Pasted%20image%2020240124102939.png)

- ChatGPT
  ![Pasted image 20240124105407](https://raw.githubusercontent.com/pyae123zd/PicoRepo/main/img/Pasted%20image%2020240124105407.png)

### 总结

- 通义灵码最为优秀
- Copilot 中规中矩
- Codeium在理解中文语义上有不足
- CodeGeex可用性不足，可以看看后续迭代