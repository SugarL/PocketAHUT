---
sidebar: false
---

# 更新日志

<div v-if="loading" class="loading">
  <span class="spinner" /> 加载中……
</div>

<div v-else-if="error" class="error-box">
  <p>加载失败：{{ error }}</p>
  <a :href="releasesUrl" target="_blank" rel="noopener noreferrer">前往 GitHub Releases 查看</a>
</div>

<template v-else>
  <div v-for="release in releases" :key="release.id" class="release">
    <h2>
      <a :href="release.html_url" target="_blank" rel="noopener noreferrer">
        {{ release.name || release.tag_name }}
      </a>
      <span v-if="release.tag_name === latestTag" class="badge badge-latest">最新</span>
      <span class="badge badge-date">{{ formatDate(release.published_at) }}</span>
      <span v-if="release.prerelease" class="badge badge-pre">预发布</span>
    </h2>
    <div class="release-body" v-html="renderMarkdown(release.body, release.assets)" />
  </div>

  <p v-if="!releases.length && !loading" class="empty">暂无发布记录。</p>
</template>

<script setup>
import { ref, onMounted } from "vue";

const OWNER = "zreason-group";
const REPO = "PocketAHUT";
const releasesUrl = `https://github.com/${OWNER}/${REPO}/releases`;

const releases = ref([]);
const latestTag = ref("");
const loading = ref(true);
const error = ref("");

onMounted(async () => {
  try {
    const res = await fetch(
      `https://api.github.com/repos/${OWNER}/${REPO}/releases?per_page=20`,
      { headers: { Accept: "application/vnd.github+json" } },
    );
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    releases.value = data;
    if (data.length) latestTag.value = data[0].tag_name;
  } catch (e) {
    error.value = e.message || "未知错误";
  } finally {
    loading.value = false;
  }
});

function formatDate(iso) {
  return new Date(iso).toLocaleDateString("zh-CN", {
    year: "numeric",
    month: "2-digit",
    day: "2-digit",
  });
}

/**
 * 极简 Markdown → HTML（仅处理 GitHub Release 常用语法）。
 * 不引入额外依赖，覆盖标题、列表、加粗、链接、代码即可。
 */
function renderMarkdown(src, assets = []) {
  if (!src) return "";
  const LT = "\x3c";
  const GT = "\x3e";

  // 构建文件名 → 下载 URL 的映射
  const downloadMap = {};
  for (const a of assets) {
    downloadMap[a.name] = a.browser_download_url;
  }

  let s = src
    // 水平线
    .replace(/^---+$/gm, `${LT}hr${GT}`)
    // 代码块
    .replace(/```(\w*)\n([\s\S]*?)```/g, `${LT}pre${GT}${LT}code class="lang-$1"${GT}$2${LT}/code${GT}${LT}/pre${GT}`)
    // 行内代码
    .replace(/`([^`]+)`/g, (m, code) => {
      if (downloadMap[code]) {
        return `${LT}a href="${downloadMap[code]}" class="apk-link"${GT}${LT}span class="icon"${GT}\uD83D\uDCE6${LT}/span${GT} ${code}${LT}/a${GT}`;
      }
      return `${LT}code${GT}${code}${LT}/code${GT}`;
    })
    // 标题
    .replace(/^### (.+)$/gm, `${LT}h3${GT}$1${LT}/h3${GT}`)
    .replace(/^## (.+)$/gm, `${LT}h2${GT}$1${LT}/h2${GT}`)
    // 加粗
    .replace(/\*\*(.+?)\*\*/g, `${LT}strong${GT}$1${LT}/strong${GT}`)
    // 链接
    .replace(/\[([^\]]+)\]\(([^)]+)\)/g, `${LT}a href="$2" target="_blank" rel="noopener noreferrer"${GT}$1${LT}/a${GT}`)
    // 无序列表（处理 \* 转义和普通 - ）
    .replace(/^\\?\* (.+)$/gm, `${LT}li${GT}$1${LT}/li${GT}`)
    .replace(/^- (.+)$/gm, `${LT}li${GT}$1${LT}/li${GT}`)
    .replace(/(${LT}li${GT}.*${LT}\/li${GT}\n?)+/g, (m) => `${LT}ul${GT}${m}${LT}/ul${GT}`)
    // 换行
    .replace(/\n/g, `${LT}br${GT}`);

  // 兜底：裸文件名如果匹配 assets 也转链接
  for (const [name, url] of Object.entries(downloadMap)) {
    const escaped = name.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
    s = s.replace(
      new RegExp(`(?<!\"|\\>)${escaped}`),
      `${LT}a href="${url}" class="apk-link"${GT}${LT}span class="icon"${GT}\uD83D\uDCE6${LT}/span${GT} ${name}${LT}/a${GT}`,
    );
  }

  return s;
}
</script>

<style scoped>
.loading {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 3rem 0;
  color: var(--vp-c-text-2);
}

.spinner {
  width: 18px;
  height: 18px;
  border: 2px solid var(--vp-c-border);
  border-top-color: var(--vp-c-brand-1);
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.error-box {
  padding: 1.5rem;
  border: 1px solid var(--vp-c-danger-1);
  border-radius: 8px;
  background: var(--vp-c-danger-soft);
  color: var(--vp-c-danger-1);
}

.error-box a {
  color: var(--vp-c-brand-1);
}

.release {
  margin-bottom: 2.5rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid var(--vp-c-border);
}

.release:last-child {
  border-bottom: none;
}

.release h2 {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
}

.release h2 a {
  color: var(--vp-c-text-1);
  text-decoration: none;
}

.release h2 a:hover {
  color: var(--vp-c-brand-1);
}

.badge {
  display: inline-block;
  padding: 0.15rem 0.55rem;
  border-radius: 10px;
  font-size: 0.75rem;
  font-weight: 500;
  vertical-align: middle;
}

.badge-latest {
  background: var(--vp-c-green-soft);
  color: var(--vp-c-green-1);
}

.badge-date {
  background: var(--vp-c-bg-soft);
  color: var(--vp-c-text-2);
}

.badge-pre {
  background: var(--vp-c-yellow-soft);
  color: var(--vp-c-yellow-1);
}

.release-body {
  font-size: 0.95rem;
  line-height: 1.7;
  color: var(--vp-c-text-2);
}

.release-body :deep(h2) {
  font-size: 1.1rem;
  margin: 1.2rem 0 0.4rem;
}

.release-body :deep(h3) {
  font-size: 1rem;
  margin: 1rem 0 0.3rem;
}

.release-body :deep(ul) {
  margin: 0.4rem 0;
  padding-left: 1.5rem;
}

.release-body :deep(li) {
  margin-bottom: 0.25rem;
}

.release-body :deep(code) {
  padding: 0.15rem 0.4rem;
  border-radius: 4px;
  font-size: 0.88em;
  background: var(--vp-c-bg-soft);
}

.release-body :deep(pre) {
  padding: 0.75rem 1rem;
  border-radius: 6px;
  overflow-x: auto;
  background: var(--vp-c-bg-soft);
}

.release-body :deep(a) {
  color: var(--vp-c-brand-1);
}

.release-body :deep(.apk-link) {
  display: inline-flex;
  align-items: center;
  gap: 0.3rem;
  padding: 0.25rem 0.75rem;
  margin-top: 0.5rem;
  border: 1px solid var(--vp-c-brand-1);
  border-radius: 6px;
  color: var(--vp-c-brand-1);
  text-decoration: none;
  font-size: 0.9rem;
  transition: background 0.15s, color 0.15s;
}

.release-body :deep(.apk-link:hover) {
  background: var(--vp-c-brand-1);
  color: var(--vp-c-bg);
}

.release-body :deep(hr) {
  margin: 1.5rem 0;
  border: none;
  border-top: 1px solid var(--vp-c-border);
}

.empty {
  padding: 3rem 0;
  text-align: center;
  color: var(--vp-c-text-3);
}
</style>
