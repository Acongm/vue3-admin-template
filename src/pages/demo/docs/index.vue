<script lang="ts" setup>
import { Packer } from "docx"
import { saveAs } from "file-saver"

// import { Component } from "vue-property-decorator"
// import { ButtonCounter } from "./button-counter.component"
import { achievements, education, experiences, skills } from "./cv-data"
import { DocumentCreator } from "./cv-generator"

const count = ref(0)

function onButtonClicked(): void {
  count.value = count.value + 1
}

function generate(): void {
  const documentCreator = new DocumentCreator()
  const doc = documentCreator.create([
    experiences,
    education,
    skills,
    achievements
  ])

  Packer.toBlob(doc).then((blob) => {
    console.log(blob)
    saveAs(blob, "example.docx")
    console.log("Document created successfully")
  })
}
</script>

<template>
  <div>
    <h1>Simple VueJs Typescript Starter</h1>
    <ul>
      <li>VueJs 2</li>
      <li>Typescript</li>
      <li>Vue Property Decorator</li>
    </ul>

    <h2>Button Component</h2>
    <ButtonCounter @clicked="onButtonClicked" :count="count" />
    <button @click="generate">
      Generate my CV with docx!
    </button>
  </div>
</template>

<style lang="scss" scoped>
.el-alert {
  margin-bottom: 20px;
}
</style>
