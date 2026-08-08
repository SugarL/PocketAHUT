---
sidebar: false
---

# 贡献者

感谢所有为 口袋安小工 做出贡献的小伙伴！🎉

<div class="contributors-grid">
  <a
    v-for="c in contributors"
    :key="c.username"
    :href="`https://github.com/${c.username}`"
    target="_blank"
    rel="noopener noreferrer"
    class="contributor-card"
  >
    <img :src="`https://avatars.githubusercontent.com/${c.uid || c.username}?v=4`" :alt="c.name" />
    <strong>{{ c.name }}</strong>
    <span>{{ c.role }}</span>
  </a>
</div>

## 捐赠感谢

感谢以下同学对 口袋安小工 的捐赠支持！排名不分先后！

<div class="donors-grid">
  <span v-for="name in donors" :key="name" class="donor-tag">{{ name }}</span>
</div>

## 成为贡献者

口袋安小工 是由安工大各个年级的学生自发组成的项目，欢迎任何形式的贡献！

- **提交代码**：Fork 项目后提交 Pull Request
- **反馈问题**：在 [GitHub Issues](https://github.com/zreason-group/PocketAHUT/issues) 提交 Bug 或建议
- **完善文档**：点击每页底部的「在 GitHub 上编辑此页」直接修改

<script setup>
const contributors = [
  { username: "domye", uid: "u/67504754", name: "Domye", role: "核心开发" },
  { username: "Feather-P", uid: "u/142159803", name: "FeatherP", role: "架构 & 维护" },
  { username: "sure141319", name: "sure", role: "开发 & 测试" },
  { username: "natoovO", name: "natoo", role: "安卓开发" },
  { username: "SugarL", name: "SugarL", role: "iOS 开发" },
  { username: "AdwanHu", name: "Adwan", role: "贡献者" },
  { username: "dingyuanyuan1100-bot", name: "jikemiao", role: "贡献者" },
  { username: "ai-li99", name: "ai-li99", role: "贡献者" },
  { username: "changantuanzi", name: "changantuanzi", role: "贡献者" },
  { username: "HydroHan", name: "Yukun Han", role: "贡献者" },
];

const donors = [
  "*厘", "*七", "(*)", "*桐", "*。", "@*y", "*之", "*明",
  "*崚", "E*e", "*酌", "**婷", "r*n", "C*e", "*🎀", "*饿",
  "*峰", "**浩", "T*i", "*印", "*速", "w*m", "*朗", "*家",
  "*郎", "*生",
];
</script>

<style scoped>
.contributors-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: 1rem;
  margin: 1.5rem 0;
}

.contributor-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.4rem;
  padding: 1.25rem 0.75rem;
  border: 1px solid var(--vp-c-border);
  border-radius: 12px;
  background: var(--vp-c-bg);
  transition: transform 0.2s, box-shadow 0.2s, border-color 0.2s;
  text-decoration: none !important;
}

.contributor-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
  border-color: var(--vp-c-brand-1);
}

.contributor-card img {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  object-fit: cover;
}

.contributor-card strong {
  font-size: 0.95rem;
  color: var(--vp-c-text-1);
  line-height: 1.3;
  text-align: center;
}

.contributor-card span {
  font-size: 0.78rem;
  color: var(--vp-c-text-3);
  line-height: 1.2;
  text-align: center;
}

.donors-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 0.6rem;
  margin: 1rem 0 2rem;
}

.donor-tag {
  display: inline-flex;
  align-items: center;
  padding: 0.35rem 0.9rem;
  border: 1px solid var(--vp-c-border);
  border-radius: 20px;
  font-size: 0.85rem;
  color: var(--vp-c-text-2);
  background: var(--vp-c-bg-soft);
  transition: transform 0.15s, box-shadow 0.15s, border-color 0.15s;
  user-select: none;
}

.donor-tag:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  border-color: var(--vp-c-brand-2);
  color: var(--vp-c-brand-1);
}
</style>
