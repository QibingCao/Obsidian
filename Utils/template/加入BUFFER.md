---
<%*
let title = tp.file.title;
if (title.startsWith("未命名")) {
 title = await tp.system.prompt("输入名称");
 if(!title) return;
}
if (title == "") {
title = "未命名";
} else {
await tp.file.rename(title);
}
await tp.file.move("/BUFFER/" + title)
-%>

类型: <% tp.system.suggester(["随想", "笔记", "问题"], ["随想", "笔记", "问题"], true, 'item')%>
创建日期: <% tp.file.creation_date("YYYY-MM-DD") %>
修改日期: <% tp.file.last_modified_date("YYYY-MM-DD") %>
---

<% tp.file.cursor() %>
