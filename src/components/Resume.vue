<template>
  <div class="resume-wrapper" :class="{ 'dark-mode': isDarkMode }">
    <div class="container-fluid px-lg-5 px-3 py-5">
      <!-- 头部区域：个人信息 + 主题切换 -->
      <div class="row mb-4 align-items-center">
        <div class="col-md-8">
          <h1 class="display-4 fw-bold">方远</h1>
          <p class="lead text-muted">前端开发工程师 / Python 开发工程师</p>
          <div class="contact-info mt-3">
            <span class="badge bg-light text-dark me-2"><i class="bi bi-gender-male"></i> 男 · 21岁</span>
            <span class="badge bg-light text-dark me-2"><i class="bi bi-telephone"></i> 13407295806</span>
            <span class="badge bg-light text-dark me-2"><i class="bi bi-envelope"></i> 3422026782@qq.com</span>
          </div>
        </div>
        <div class="col-md-4 text-md-end mt-3 mt-md-0">
          <!-- 头像区域 -->
    <img 
      src="/person.jpg" 
      alt="个人照片" 
      class="profile-avatar"
      @error="handleImageError"
    >
    <!-- 主题切换按钮 -->
    <button class="btn btn-outline-secondary" @click="toggleTheme">
      <i :class="isDarkMode ? 'bi bi-sun' : 'bi bi-moon'"></i> {{ isDarkMode ? '亮色模式' : '暗色模式' }}
    </button>
        </div>
      </div>

      <!-- 求职意向卡片 -->
      <div class="card shadow-sm mb-4">
        <div class="card-body">
          <h5 class="card-title"><i class="bi bi-briefcase"></i> 求职意向</h5>
          <p class="card-text fs-5">前端开发工程师 / Python 开发工程师 · 全职</p>
        </div>
      </div>

      <!-- 自我评价 + 技能区域（两列） -->
      <div class="row g-4 mb-4">
        <div class="col-lg-5">
          <div class="card h-100 shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-person-lines-fill"></i> 自我评价</h5>
              <p class="card-text">{{ evaluation }}</p>
            </div>
          </div>
        </div>
        <div class="col-lg-7">
          <div class="card h-100 shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-code-slash"></i> 专业技能</h5>
              <div v-for="group in skillGroups" :key="group.name" class="mb-3">
                <strong>{{ group.name }}</strong>
                <div class="mt-1">
                  <SkillBadge
                    v-for="skill in group.skills"
                    :key="skill.name"
                    :skill="skill"
                    @click="handleSkillClick(skill)"
                  />
                </div>
              </div>
              <div v-if="selectedSkill" class="alert alert-info mt-3">
                <i class="bi bi-info-circle"></i> 详情：{{ selectedSkill.detail }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 项目经历 + 教育/证书等（两列布局） -->
      <div class="row g-4 mb-4">
        <div class="col-lg-7">
          <div class="card shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-folder2-open"></i> 项目经历</h5>
              <ProjectCard
                v-for="project in projects"
                :key="project.id"
                :project="project"
                @show-detail="showProjectDetail"
              />
            </div>
          </div>
        </div>
        <div class="col-lg-5">
          <!-- 教育经历 -->
          <div class="card shadow-sm mb-4">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-mortarboard"></i> 教育经历</h5>
              <p><strong>武汉华夏理工学院 · 通信工程专业</strong> 本科<br>2021.09 - 2026.06</p>
            </div>
          </div>
          <!-- 资质证书 -->
          <div class="card shadow-sm mb-4">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-award"></i> 资质证书</h5>
              <ul class="list-unstyled">
                <li v-for="cert in certificates" :key="cert"><i class="bi bi-check-circle-fill text-success me-2"></i> {{ cert }}</li>
              </ul>
            </div>
          </div>
          <!-- 竞赛与荣誉 -->
          <div class="card shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-trophy"></i> 竞赛与荣誉</h5>
              <ul class="list-unstyled">
                <li v-for="honor in honors" :key="honor"><i class="bi bi-star-fill text-warning me-2"></i> {{ honor }}</li>
              </ul>
            </div>
          </div>
        </div>
      </div>

      <!-- 校园经历 -->
      <div class="row">
        <div class="col">
          <div class="card shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-people"></i> 校园经历</h5>
              <p><strong>学校体育舞蹈队 成员</strong> | 2022.09 - 2025.06</p>
              <p>具备优秀的团队协作、沟通表达、执行力与抗压能力，多次参与省级赛事并获奖，做事专注、目标感强。</p>
            </div>
          </div>
        </div>
      </div>

      <!-- 项目详情弹窗 -->
      <div v-if="selectedProject" class="mt-4">
        <div class="alert alert-secondary">
          <strong>项目详情：</strong> {{ selectedProject.detail }}
        </div>
      </div>

      <!-- 点击统计 -->
      <div class="text-center text-muted mt-3 small">
        <i class="bi bi-hand-index-thumb"></i> 技能点击次数：{{ skillClickCount }} 次
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, watch, onMounted } from 'vue'
// 引入拆分后的子组件
import SkillBadge from './SkillBadge.vue'
import ProjectCard from './ProjectCard.vue'

// ----- 响应式数据 -----
const isDarkMode = ref(false)
const selectedSkill = ref(null)
const selectedProject = ref(null)
const skillClickCount = ref(0)

// 自我评价
const evaluation = `我具备前端开发 + Python 后端开发双重技术能力，熟练掌握 Vue3 全家桶、HTML/CSS/JS/ES6+、jQuery、Bootstrap 等前端技术栈，同时精通 Python、Django 框架、网络编程与 MySQL 数据库操作，可独立完成前后端项目开发。拥有通信工程专业背景，逻辑思维清晰、学习能力强、做事严谨，具备优秀的团队协作、抗压能力与问题解决能力，希望在前端 / Python 开发方向深耕发展，快速成长为全能型开发人员。`

// 技能分组
const skillGroups = reactive([
  {
    name: '前端核心技能',
    skills: [
      { name: 'HTML5/CSS3/ES6+', detail: '精通页面布局、Flex/Grid、响应式开发、兼容处理' },
      { name: 'Vue3', detail: '熟练组合式 API、响应式数据、生命周期、组件通信' },
      { name: 'jQuery/Bootstrap', detail: '熟练使用快速开发页面' },
      { name: 'Vue Router/Pinia', detail: '掌握状态管理与路由' }
    ]
  },
  {
    name: 'Python 开发技能',
    skills: [
      { name: 'Python 基础', detail: '熟练语法、数据结构、文件操作、多线程' },
      { name: 'Django 框架', detail: '独立完成后端项目搭建、接口开发、业务逻辑实现' },
      { name: '网络编程', detail: '掌握 TCP/UDP 通信、Socket 编程' },
      { name: 'MySQL', detail: '熟练数据库设计、增删改查、联表查询、索引优化' }
    ]
  },
  {
    name: '其他技术能力',
    skills: [
      { name: 'Java 基础', detail: '熟悉 Java 基础语法与数据库操作' },
      { name: '5G 网络部署', detail: '掌握 XLAB 仿真系统操作' },
      { name: '嵌入式开发', detail: 'ARM-Linux 开发、硬件调试能力' }
    ]
  }
])

// 项目数据
const projects = reactive([
  {
    id: 1,
    name: '嵌入式光感智控系统设计',
    date: '2023.09 - 2024.01',
    summary: '硬件端完成器件选型与线路连接，软件端基于 ARM-Linux 开发驱动与应用程序，实现光照智能控制与数据传输。',
    detail: '全程独立完成系统设计、搭建与调试，精准解决软硬件兼容问题，强化嵌入式全栈落地能力。'
  },
  {
    id: 2,
    name: '5G 网络部署开通与业务体验（课程设计）',
    date: '2024.02 - 2024.06',
    summary: '依托 XLAB5G 仿真系统，独立完成 5G 网络搭建、参数配置、业务开通与功能测试。',
    detail: '对 5G 网络各项业务性能指标进行测试、记录与分析，形成完整技术分析报告。'
  },
  {
    id: 3,
    name: '企业人事管理系统开发（Java 数据库实训）',
    date: '2023.03 - 2023.06',
    summary: '融合 Java 与 MySQL，完成系统架构设计，实现员工信息增删改查、权限管理等核心功能。',
    detail: '开发出可落地的企业员工人事管理系统，提升了编程与系统设计能力。'
  }
])

const certificates = ['计算机二级证书', '普通话二级乙等证书', 'C1 驾驶证']
const honors = [
  '2025 “大唐杯” 全国大学生移动通信 5G 技术大赛 省级三等奖',
  '2023 湖北省大学生体育舞蹈锦标赛 省级二等奖',
  '2024 湖北省大学生体育舞蹈锦标赛 项目组第八名',
  '2023-2024 学年 青年文艺标兵'
]

// ----- 计算属性 -----
const skillCount = computed(() => {
  return skillGroups.reduce((total, group) => total + group.skills.length, 0)
})

// ----- 生命周期 -----
onMounted(() => {
  console.log('简历页面已加载，共展示技能数：', skillCount.value)
})

// ----- 监听 -----
watch(skillClickCount, (newVal, oldVal) => {
  console.log(`技能点击次数从 ${oldVal} 变为 ${newVal}`)
})

// ----- 方法 -----
const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
}

const handleSkillClick = (skill) => {
  selectedSkill.value = skill
  skillClickCount.value++
}

const showProjectDetail = (project) => {
  selectedProject.value = project
}
</script>

<style scoped>
/* 引入Bootstrap Icons */
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css");


/* 头像基础样式 */
.profile-avatar {
  width: 150px;
  height: 150px;
  border-radius: 50%;          /* 圆形 */
  object-fit: cover;           /* 确保图片比例不变形，裁剪填充 */
  border: 2px solid #fff;
  box-shadow: 0 4px 10px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
  background-color: #f0f0f0;   /* 占位背景色 */
  margin: 25px;
}

/* 悬停效果 */
.profile-avatar:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 15px rgba(0,0,0,0.15);
  border-color: #ffc107;
}

/* 暗色模式适配 */
.dark-mode .profile-avatar {
  border-color: #444;
  box-shadow: 0 4px 10px rgba(0,0,0,0.3);
}
.dark-mode .profile-avatar:hover {
  border-color: #ffc107;
}

/* 暗色模式样式 */
.resume-wrapper {
  transition: background-color 0.3s ease;
  background-color: #f8f9fa;
  width: 100%;
  min-height: 100vh;
  margin: 0;
  padding: 0;
}
.resume-wrapper.dark-mode {
  background-color: #121212;
  color: #e0e0e0;
}
.dark-mode .card {
  background-color: #1e1e1e;
  border-color: #333;
  color: #e0e0e0;
}
.dark-mode .card .text-muted {
  color: #aaa !important;
}
.dark-mode .btn-outline-primary {
  color: #6ea8fe;
  border-color: #6ea8fe;
}
.dark-mode .btn-outline-primary:hover {
  background-color: #6ea8fe;
  color: #121212;
}
.dark-mode .alert-info {
  background-color: #2c3e50;
  border-color: #2c3e50;
  color: #bcd0f7;
}
.dark-mode .alert-secondary {
  background-color: #2d2d2d;
  border-color: #2d2d2d;
  color: #ccc;
}
.dark-mode .badge.bg-light {
  background-color: #333 !important;
  color: #fff !important;
}
.dark-mode .btn-outline-secondary {
  color: #ccc;
  border-color: #666;
}
.dark-mode .btn-outline-secondary:hover {
  background-color: #666;
  color: #fff;
}
</style>