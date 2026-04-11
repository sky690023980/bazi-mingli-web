<template>
  <div class="page">
    <!-- 背� ��装饰 -->
    <div class="bg-decor">
       <div class="bagua-bg-1">☰</div>
      <div  class="bagua-bg-2">☷</div>
      <div clas s="bagua-bg-3">☱</div>
      <div class="ba gua-bg-4">☲</div>
    </div>

    <!-- Hero  -->
    <header class="hero">
      <div cla ss="hero-bagua">☯</div>
      <h1 class="he ro-title">易学命理</h1>
      <p class="h ero-subtitle">八字排盘 · 六爻解读 ·  紫微奇门 · LLM 智能分析</p>
      < div class="hero-divider"></div>
    </header> 

    <!-- 主表单卡片 -->
    <section c lass="card form-card">
      <!-- 姓名 -->
       <div class="field-row">
        <span c lass="field-icon">✦</span>
        <label c lass="field-label">姓名</label>
        <in put class="field-input" v-model="name" placeh older="选填，便于记录" />
      </div> 
      <div class="divider"></div>

      <!- - 性别 -->
      <div class="field-row">
         <span class="field-icon">⚥</span>
         <label class="field-label">性别</label >
        <div class="gender-switch">
           <button :class="['gender-tab', gender ===  'M' && 'active']" @click="gender = 'M'">
             <span class="gender-gua">乾</span><s pan>男</span>
          </button>
           <button :class="['gender-tab', gender === 'F'  && 'active']" @click="gender = 'F'">
             <span class="gender-gua">坤</span><span >女</span>
          </button>
        </div >
      </div>
      <div class="divider"></d iv>

      <!-- 出生时间 -->
      <div c lass="section-header">
        <span class="s ection-icon">🗓</span>
        <span class= "section-title">出生时间</span>
        < span class="section-hint">公历时间</span> 
      </div>
      <div class="birth-grid">
         <div class="picker-cell">
          < span class="picker-label">年</span>
           <select v-model="yearIdx" @change="onYearCh ange">
            <option v-for="(y, i) in y ears" :key="y" :value="i">{{ y }}</option>
           </select>
        </div>
        <div  class="picker-cell">
          <span class=" picker-label">月</span>
          <select v- model="monthIdx" @change="onMonthChange">
             <option v-for="(m, i) in months" :ke y="m" :value="i">{{ m }}</option>
          < /select>
        </div>
        <div class="p icker-cell">
          <span class="picker-la bel">日</span>
          <select v-model="da yIdx">
            <option v-for="(d, i) in d ays" :key="d" :value="i">{{ d }}</option>
           </select>
        </div>
        <div  class="picker-cell">
          <span class="p icker-label">时</span>
          <select v-m odel="hourIdx" @change="onHourChange">
             <option v-for="(h, i) in hours" :key="h " :value="i">{{ h }}</option>
          </sel ect>
        </div>
      </div>
      <div c lass="shichen-hint">
        <span>☰ {{ shi chenName }}时 · {{ shichenGua }}卦</span>
       </div>
      <div class="divider"></div >

      <!-- 出生地 -->
      <div class= "field-row">
        <span class="field-icon" >✧</span>
        <label class="field-label ">出生地</label>
        <input class="fie ld-input" v-model="location" placeholder="可 不填" />
      </div>
    </section>

    < !-- 解读风格 -->
    <section class="card  options-card">
      <div class="section-hea der">
        <span class="section-icon">✧< /span>
        <span class="section-title">� �读风格</span>
      </div>
      <div cla ss="style-grid">
        <div
          v-for ="(s, i) in styles"
          :key="s.key"
           :class="['style-item', styleIdx === i  && 'active']"
          @click="styleIdx = i "
        >
          <span class="style-icon ">{{ s.icon }}</span>
          <span class=" style-name">{{ s.name }}</span>
          <sp an class="style-desc">{{ s.desc }}</span>
         </div>
      </div>
      <div class="di vider"></div>

      <!-- 占事提问 -->
       <div class="field-row vertical">
         <div class="field-row-title">
          <span  class="field-icon">❖</span>
          <spa n class="field-label">占事 / 提问</span>
           <span class="field-hint">选填，� ��想问什么？</span>
        </div>
         <textarea
          class="field-textarea" 
          v-model="ask"
          placeholde r="例如：我的事业发展方向如何？ 今年财运怎样？"
          maxlength="2 00"
          rows="3"
        ></textarea>
       </div>
    </section>

    <!-- 错误� �示 -->
    <div v-if="error" class="error-t ip">
      <span>⚠ {{ error }}</span>
    < /div>

    <!-- 提交按钮 -->
    <button  class="btn-submit" :disabled="loading" @click ="onSubmit">
      <span v-if="!loading" clas s="btn-content">
        <span>☯</span>
         <span>开始排盘</span>
        <span> →</span>
      </span>
      <span v-else c lass="loading-wrap">
        <span class="bag ua-loading">☯</span>
        <span>正在� �盘，请稍候...</span>
      </span>
     </button>

    <!-- 底部 -->
    <footer cl ass="footer">
      <p>☯ 易学命理 · � �供娱乐参考</p>
    </footer>
  </div>
< /template>

<script setup>
import { ref, comp uted, onMounted } from 'vue'
import { useRout er } from 'vue-router'

const router = useRou ter()

// 状态
const name = ref('')
const g ender = ref('M')
const yearIdx = ref(0)
const  monthIdx = ref(0)
const dayIdx = ref(0)
cons t hourIdx = ref(0)
const location = ref('')
c onst styleIdx = ref(0)
const ask = ref('')
co nst loading = ref(false)
const error = ref('' )

// 选择器数据
const years = ref([])
c onst months = Array.from({ length: 12 }, (_,  i) => `${i + 1}月`)
const days = ref([])
con st hours = Array.from({ length: 24 }, (_, i)  => `${i}时`)

const styles = [
  { key: 'pro fessional', icon: '📜', name: '专业详细 ', desc: '术语丰富，深入解读命理'  },
  { key: 'simple',      icon: '☯', name:  '简洁明了', desc: '精炼概括，直击 核心' },
  { key: 'plain',        icon: '� ��', name: '通俗易懂', desc: '白话解� �，轻松理解' },
]

// 时辰映射
const  SHICHEN_MAP = [
  { name: '子', gua: '坎'  }, { name: '丑', gua: '艮' }, { name: '寅' , gua: '艮' },
  { name: '卯', gua: '震' } , { name: '辰', gua: '巽' }, { name: '巳',  gua: '巽' },
  { name: '午', gua: '离' },  { name: '未', gua: '坤' }, { name: '申',  gua: '坤' },
  { name: '酉', gua: '兑' },  { name: '戌', gua: '乾' }, { name: '亥', g ua: '乾' },
]

const shichenName = computed( () => SHICHEN_MAP[hourIdx.value % 12]?.name | | '子')
const shichenGua = computed(() => SH ICHEN_MAP[hourIdx.value % 12]?.gua || '坎')
 
// 生成某年某月的天数
function getD aysInMonth(year, month) {
  return new Date(y ear, month, 0).getDate()
}

function initYear s() {
  const cur = new Date().getFullYear()
   years.value = Array.from({ length: 80 }, (_ , i) => `${cur - 70 + i}`)
  days.value = Arr ay.from({ length: getDaysInMonth(+years.value [0], 1) }, (_, i) => `${i + 1}日`)
}

functi on onYearChange() {
  const y = +years.value[ yearIdx.value]
  const m = monthIdx.value + 1 
  days.value = Array.from({ length: getDaysI nMonth(y, m) }, (_, i) => `${i + 1}日`)
  if  (dayIdx.value >= days.value.length) dayIdx.v alue = days.value.length - 1
}

function onMo nthChange() {
  const y = +years.value[yearId x.value]
  const m = monthIdx.value + 1
  day s.value = Array.from({ length: getDaysInMonth (y, m) }, (_, i) => `${i + 1}日`)
  if (dayI dx.value >= days.value.length) dayIdx.value =  days.value.length - 1
}

function onHourChan ge() {}

onMounted(initYears)

// API 基础� ��址
const API_BASE = import.meta.env.VITE_A PI_BASE || 'http://localhost:8000'

async fun ction onSubmit() {
  error.value = ''
  if (l oading.value) return

  const year = +years.v alue[yearIdx.value]
  const month = monthIdx. value + 1
  const day = dayIdx.value + 1
  co nst hour = hourIdx.value

  loading.value = t rue

  try {
    const res = await fetch(`${A PI_BASE}/api/extended/full-analysis`, {
       method: 'POST',
      headers: { 'Content-Ty pe': 'application/json' },
      body: JSON.s tringify({ year, month, day, hour, gender: ge nder.value, location: location.value }),
     })
    const json = await res.json()

    if  (json.code === 200) {
      const payload = { 
        name: name.value,
        gender: ge nder.value === 'M' ? '男' : '女',
        a sk: ask.value,
        style: styles[styleIdx .value].key,
        data: json.data,
      } 
      // 编码传给 result 页面
      co nst encoded = btoa(unescape(encodeURIComponen t(JSON.stringify(payload))))
      router.pus h({ path: '/result', query: { d: encoded } }) 
    } else {
      error.value = json.msg ||  '请求失败'
    }
  } catch (e) {
    err or.value = '无法连接到后端服务，请 确保 http://localhost:8000 已启动'
  } f inally {
    loading.value = false
  }
}
</sc ript>

<style scoped>
.page {
  min-height: 1 00vh;
  background: #0D0F14;
  padding: 0 16p x 100px;
  position: relative;
  overflow-x:  hidden;
  max-width: 600px;
  margin: 0 auto; 
}

/* 背景 */
.bg-decor { position: fixed;  top: 0; left: 0; right: 0; bottom: 0; pointe r-events: none; overflow: hidden; z-index: 0;  }
.bagua-bg-1, .bagua-bg-2, .bagua-bg-3, .ba gua-bg-4 {
  position: absolute; font-size: 1 80px; color: #C9A84C; opacity: 0.03; font-wei ght: 100;
}
.bagua-bg-1 { top: 60px; left: -3 0px; transform: rotate(15deg); }
.bagua-bg-2  { bottom: 200px; right: -30px; transform: rot ate(-20deg); font-size: 260px; }
.bagua-bg-3  { top: 40%; left: 50%; transform: translate(- 50%,-50%) rotate(45deg); font-size: 220px; }
 .bagua-bg-4 { bottom: -40px; left: 15%; font- size: 150px; transform: rotate(30deg); }

/*  Hero */
.hero { position: relative; z-index:  1; text-align: center; padding: 56px 0 32px;  }
.hero-bagua { font-size: 56px; color: #C9A8 4C; animation: float 4s ease-in-out infinite;  display: block; margin-bottom: 8px; }
.hero- title { font-size: 28px; color: #C9A84C; lett er-spacing: 4px; margin-bottom: 8px; }
.hero- subtitle { font-size: 13px; color: #888; lett er-spacing: 1px; }
.hero-divider { width: 60p x; height: 2px; background: linear-gradient(9 0deg,transparent,#C9A84C,transparent); margin : 16px auto 0; }

@keyframes float {
  0%, 10 0% { transform: translateY(0); }
  50% { tran sform: translateY(-6px); }
}

/* 卡片 */
.c ard {
  position: relative; z-index: 1;
  bac kground: rgba(255,255,255,0.03);
  border: 1p x solid rgba(201,168,76,0.15);
  border-radiu s: 16px;
  padding: 20px;
  margin-bottom: 16 px;
  backdrop-filter: blur(8px);
}
.form-car d { animation: slideUp 0.6s ease both; }
.opt ions-card { animation: slideUp 0.6s 0.15s eas e both; }

@keyframes slideUp {
  from { opac ity: 0; transform: translateY(24px); }
  to {  opacity: 1; transform: translateY(0); }
}

/ * 字段 */
.field-row { display: flex; align -items: center; gap: 10px; padding: 4px 0; }
 .field-row.vertical { flex-direction: column;  align-items: flex-start; gap: 8px; }
.field- row-title { display: flex; align-items: cente r; gap: 8px; width: 100%; }
.field-icon { col or: #C9A84C; font-size: 16px; flex-shrink: 0;  }
.field-label { color: #C0B498; font-size:  14px; flex-shrink: 0; min-width: 48px; }
.fie ld-hint { color: #666; font-size: 12px; margi n-left: auto; }
.field-input {
  flex: 1; bac kground: transparent; border: none; border-bo ttom: 1px solid rgba(201,168,76,0.2);
  color : #E8E0D0; font-size: 14px; padding: 4px 0; o utline: none; transition: border-color 0.3s;
 }
.field-input:focus { border-bottom-color: # C9A84C; }
.field-input::placeholder { color:  #555; }
select.field-input { border: 1px soli d rgba(201,168,76,0.2); border-radius: 8px; p adding: 6px 10px; background: rgba(201,168,76 ,0.05); cursor: pointer; }
.field-textarea {
   width: 100%; background: rgba(201,168,76,0. 04); border: 1px solid rgba(201,168,76,0.15); 
  border-radius: 10px; color: #E8E0D0; font- size: 14px; padding: 10px; resize: vertical;
   outline: none; font-family: inherit; line-h eight: 1.6;
}
.field-textarea::placeholder {  color: #555; }
.field-textarea:focus { border -color: rgba(201,168,76,0.4); }

.divider { h eight: 1px; background: rgba(255,255,255,0.05 ); margin: 14px 0; }

/* 性别 */
.gender-sw itch { display: flex; gap: 8px; margin-left:  auto; }
.gender-tab {
  display: flex; align- items: center; gap: 4px; padding: 6px 14px; b order-radius: 20px;
  border: 1px solid rgba( 201,168,76,0.2); background: transparent; col or: #888; cursor: pointer;
  font-size: 13px;  transition: all 0.3s;
}
.gender-tab .gender- gua { font-size: 15px; }
.gender-tab.active {  background: rgba(201,168,76,0.15); border-co lor: #C9A84C; color: #C9A84C; }

/* 出生时 间 */
.section-header { display: flex; align -items: center; gap: 8px; margin-bottom: 12px ; }
.section-icon { font-size: 14px; }
.secti on-title { color: #C0B498; font-size: 14px; } 
.section-hint { color: #666; font-size: 12px ; margin-left: auto; }
.birth-grid { display:  grid; grid-template-columns: repeat(4, 1fr);  gap: 8px; }
.picker-cell { display: flex; fl ex-direction: column; gap: 4px; }
.picker-lab el { color: #666; font-size: 11px; text-align : center; }
select.picker-cell {
  background : rgba(201,168,76,0.06); border: 1px solid rg ba(201,168,76,0.15);
  border-radius: 8px; co lor: #E8E0D0; font-size: 13px; padding: 6px 4 px; cursor: pointer; text-align: center;
  ou tline: none; appearance: none; -webkit-appear ance: none;
  background-image: url("data:ima ge/svg+xml,%3Csvg xmlns='http://www.w3.org/20 00/svg' width='10' height='6'%3E%3Cpath d='M0  0l5 6 5-6z' fill='%23C9A84C'/%3E%3C/svg%3E") ;
  background-repeat: no-repeat; background- position: right 8px center; padding-right: 24 px;
}
select.picker-cell option { background:  #1a1d24; color: #E8E0D0; }
.shichen-hint { t ext-align: center; margin-top: 8px; color: #C 9A84C; font-size: 13px; }

/* 风格 */
.styl e-grid { display: grid; grid-template-columns : repeat(3, 1fr); gap: 8px; }
.style-item {
   display: flex; flex-direction: column; align -items: center; gap: 4px; padding: 12px 4px;
   border: 1px solid rgba(255,255,255,0.06); b order-radius: 12px; cursor: pointer;
  transi tion: all 0.3s; background: transparent;
}
.s tyle-item.active {
  border-color: #C9A84C; b ackground: rgba(201,168,76,0.08);
}
.style-ic on { font-size: 20px; }
.style-name { color:  #C9A84C; font-size: 12px; font-weight: 500; } 
.style-desc { color: #666; font-size: 10px;  text-align: center; line-height: 1.3; }
.styl e-item.active .style-name { color: #C9A84C; } 

/* 按钮 */
.btn-submit {
  display: flex;  align-items: center; justify-content: center ; gap: 8px;
  width: 100%; padding: 16px; bac kground: linear-gradient(135deg, #C9A84C, #8B 6914);
  border: none; border-radius: 14px; c olor: #0D0F14; font-size: 16px; font-weight:  700;
  cursor: pointer; position: relative; z -index: 1; margin-bottom: 16px;
  transition:  transform 0.2s, box-shadow 0.2s; letter-spac ing: 2px;
}
.btn-submit:active { transform: s cale(0.98); }
.btn-submit:disabled { opacity:  0.7; cursor: not-allowed; }
.btn-content, .l oading-wrap { display: flex; align-items: cen ter; gap: 8px; }
.bagua-loading {
  display:  inline-block; font-size: 20px; color: #0D0F14 ;
  animation: spin 2s linear infinite;
}
@ke yframes spin { from { transform: rotate(0deg) ; } to { transform: rotate(360deg); } }

/* � ��误 */
.error-tip { color: #ff6b6b; font-si ze: 13px; text-align: center; margin-bottom:  12px; padding: 8px; border: 1px solid rgba(25 5,107,107,0.2); border-radius: 8px; }

/* 底 部 */
.footer { text-align: center; color: # 555; font-size: 12px; padding: 16px 0; }
</st yle>
 