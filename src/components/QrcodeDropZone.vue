<template>
  <div
    @drop.prevent.stop="onDrop"
    @dragenter.prevent.stop="onDragOver(true)"
    @dragleave.prevent.stop="onDragOver(false)"
    @dragover.prevent.stop
  >
    <slot></slot>
  </div>
</template>

<script setup lang="ts">
import { processFile, processUrl } from '../misc/scanner'

const emit = defineEmits(['detect', 'dragover', 'error'])

// methods
const onDetect = async promise => {
  try {
    const detectedCodes = await promise
    emit('detect', detectedCodes)
  } catch (error) {
    emit('error', error)
  }
}

const onDragOver = (isDraggingOver: boolean) => {
  emit('dragover', isDraggingOver)
}

const onDrop = ({ dataTransfer }: DragEvent) => {
  if (!dataTransfer) return

  onDragOver(false)

  const droppedFiles = [...Array.from(dataTransfer.files)]
  const droppedUrl = dataTransfer.getData('text/uri-list')

  droppedFiles.forEach((file: File) => {
    onDetect(processFile(file))
  })

  if (droppedUrl !== '') {
    onDetect(processUrl(droppedUrl))
  }
}
</script>
