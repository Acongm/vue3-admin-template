<script lang="ts" setup>
import type { LoadData } from "pizzip"
import { renderAsync } from "docx-preview"
import Docxtemplater from "docxtemplater"
import { saveAs } from "file-saver"
import PizZip from "pizzip"

const previewContainer = ref()
const docsData = ref({ name: "张三", date: "2025-06-06" })
const docxUrl = ref("/template.docx")
// const handleRendered = () => console.log("渲染完成")
function fetchTempDocs(url: string): Promise<ArrayBuffer> {
  return new Promise((resolve, reject) => {
    fetch(url).then((res) => {
      if (!res.ok) {
        // throw new Error(`模板加载失败: ${res.status}`)
        reject(res)
        return
      }
      resolve(res.arrayBuffer())
    }).catch((error) => {
      console.error("模板加载异常:", error)
      reject(error)
    })
  })
}
async function generateDocxBlob() {
  const templateBuffer: LoadData = await fetchTempDocs(docxUrl.value)
  const zip = new PizZip(templateBuffer)
  const doc = new Docxtemplater().loadZip(zip)
  doc.setOptions({ nullGetter: () => "-" })
  doc.setData(docsData.value)
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
async function exportToDocx() {
  try {
    const blob = await generateDocxBlob()
    saveAs(blob, "合同.docx")
  } catch (error) {
    console.error("文档生成失败:", error)
  }
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
