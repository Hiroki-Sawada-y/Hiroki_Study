
```dataviewjs
let tableData = [];

// 获取具有标签 #race 的文件路径
for (let note of dv.pagePaths('"Bughunter-report" and #race')) {
    dv.io.load(note).then((fileContent) => {
        if (fileContent !== undefined) {
            // 将文件内容按行分割成数组
            let lines = fileContent.split('\n');
            let Markdown =dv.fileLink(note)
            let title = ''; // 存储二级标题作为 "Title"
            let summaryContent = '';
            let stepContent = '';
            let impactContent = '';
				let count = 0;
            // 存储当前正在处理的三级标题
            let currentSection = '';

            // 遍历每一行文本
            for (let i = 0; i < lines.length; i++) {
                let line = lines[i];
                if (line.startsWith('## ') && lines[i + 1] && lines[i + 1].includes('#race')) {
                    // 如果是满足条件的二级标题，将其作为 "Title"
                    title = line.replace('## ', '');
                    continue;
                } else if (line.startsWith('### Summary')) {
                    currentSection = 'Summary';
                    summaryContent = '';
                } else if (line.startsWith('### Step')) {
                    currentSection = 'Step';
                    stepContent = '';
                } else if (line.startsWith('### Impact')) {
                    currentSection = 'Impact';
                    impactContent = '';
                } else if (line.startsWith('### ')) { 
                    // 其他三级标题，跳过处理 
                    currentSection = '';
                }
                
                // 在此处将处理内容的部分与行同级
                if (currentSection === 'Summary') {
                    summaryContent += line + '\n';
                } else if (currentSection === 'Step') {
                    stepContent += line + '\n';
                } else if (currentSection === 'Impact') {
                    impactContent += line + '\n';
                    count ++ ;
                }

                // 判断是否推送数据
                if ((lines[i+1].startsWith('## ') && lines[i + 2] && lines[i + 2].includes('#race')) || (i === lines.length - 2)) {
                    // 将标题和内容添加到表格数据
                    tableData.push([Markdown, title, summaryContent.trim(), stepContent.trim(), impactContent.trim()]);
                    title = ''; // 存储二级标题作为 "Title"
                    summaryContent = '';
                    stepContent = '';
                    impactContent = '';
                    count = 0; // 重置 count 的值
                }
            }
        }
    });
}

// 创建包含四列的表格，"Markdown","Title", "Summary", "Step" 和 "Impact"
setTimeout(() => {
    dv.table(["Markdown","Title", "Summary", "Step", "Impact"], tableData);
}, 1000); // 使用适当的延迟时间等待异步操作完成


```