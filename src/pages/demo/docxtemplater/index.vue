<script lang="ts" setup>
import { renderAsync } from "docx-preview"
import Docxtemplater from "docxtemplater"
import { saveAs } from "file-saver"
import PizZip from "pizzip"

const previewContainer = ref()
const docxUrl = ref("/template.docx")
// const handleRendered = () => console.log("渲染完成")
async function generateDocxBlob() {
  const templateBuffer = await fetch(docxUrl.value).then(res => res.arrayBuffer())
  const zip = new PizZip(templateBuffer)
  const doc = new Docxtemplater().loadZip(zip)
  doc.setData({ name: "张三", date: "2025-06-06" })
  doc.render()
  return doc.getZip().generate({
    type: "blob",
    mimeType: "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
  })
}
async function previewDocx() {
  const blob = await generateDocxBlob()
  await renderAsync(blob, previewContainer.value)
}
function exportToDocx() {
  fetch(docxUrl.value)
    .then((res) => {
      if (!res.ok) throw new Error(`模板加载失败: ${res.status}`)
      return res.arrayBuffer()
    })
    .then((buffer) => {
      try {
        const zip = new PizZip(buffer) // ✅ 同步加载
        const doc = new Docxtemplater().loadZip(zip)

        // 处理未定义值（避免显示 "undefined"）
        doc.setOptions({ nullGetter: () => "" })

        // 填充数据
        doc.setData({ name: "张三", date: "2025-06-06" })
        doc.render()

        // 生成并下载
        const blob = doc.getZip().generate({
          type: "blob",
          mimeType: "application/vnd.openxmlformats-officedocument.wordprocessingml.document" // ✅ 指定 MIME
        })
        saveAs(blob, "合同.docx")
      } catch (error) {
        console.error("文档生成失败:", error)
      }
    })
    .catch(error => console.error("模板加载异常:", error))
}

onMounted(async () => {
  await previewDocx()
})
</script>

<template>
  <div class="app-container">
    <vxe-button @click="exportToDocx()">
      导出docs
    </vxe-button>

    <div ref="previewContainer" class="docx-preview" />
  </div>
</template>

<style lang="scss" scoped>
.el-alert {
  margin-bottom: 20px;
}
</style>
