<template>
  <div ref="giscusContainer" class="giscus-container" />
</template>

<script setup>
import { useData, useRoute } from "vitepress";
import { nextTick, ref, watch } from "vue";

const route = useRoute();
const { isDark } = useData();
const giscusContainer = ref(null);

function loadGiscus() {
  if (!giscusContainer.value) return;
  giscusContainer.value.innerHTML = "";

  const script = document.createElement("script");
  script.src = "https://giscus.app/client.js";
  script.setAttribute("data-repo", "Liiked/blog");
  script.setAttribute("data-repo-id", "MDEwOlJlcG9zaXRvcnkzNTEyOTg0MzY=");
  script.setAttribute("data-category", "Announcements");
  script.setAttribute("data-category-id", "DIC_kwDOFPBjhM4DFWde");
  script.setAttribute("data-mapping", "title");
  script.setAttribute("data-strict", "0");
  script.setAttribute("data-reactions-enabled", "1");
  script.setAttribute("data-emit-metadata", "0");
  script.setAttribute("data-input-position", "bottom");
  script.setAttribute("data-theme", isDark.value ? "dark" : "light");
  script.setAttribute("data-lang", "zh-CN");
  script.crossOrigin = "anonymous";
  script.async = true;

  giscusContainer.value.appendChild(script);
}

// giscus 通过 postMessage 接收主题变更，避免每次切换明暗都重新加载 iframe
function setGiscusTheme(dark) {
  const iframe = document.querySelector("iframe.giscus-frame");
  if (!iframe) return;
  iframe.contentWindow.postMessage(
    { giscus: { setConfig: { theme: dark ? "dark" : "light" } } },
    "https://giscus.app"
  );
}

// 路由切换时（如点击侧边栏文章链接）重新挂载评论区，因为 VitePress 是 SPA
watch(
  () => route.path,
  () => nextTick(loadGiscus),
  { immediate: true }
);

watch(isDark, (dark) => setGiscusTheme(dark));
</script>

<style>
.giscus-container {
  margin-top: 2rem;
}
</style>
