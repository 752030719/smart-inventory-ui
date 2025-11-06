<script setup>
import { computed, nextTick, onMounted, reactive, ref } from 'vue'

const filters = reactive({
  material: '10000293',
  plant: '1010',
  targetWarehouse: '404I',
  targetQuantity: '20'
})

const aiRecommendations = ref([
  {
    key: 'row-1',
    priority: 1,
    available: true,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '402G',
    storageName: '南昌售后库',
    availableQuantity: '20 EA',
    duration: '58 H',
    amount: '720 RMB',
    reason: '各项最优'
  },
  {
    key: 'row-2',
    priority: 2,
    available: true,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '406K',
    storageName: '贵阳售后库',
    availableQuantity: '50 EA',
    duration: '76 H',
    amount: '800 RMB',
    reason: '物料时长比南昌售后库长'
  },
  {
    key: 'row-3',
    priority: 3,
    available: true,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '202B',
    storageName: '北京仓库',
    availableQuantity: '2000 EA',
    duration: '34 H',
    amount: '600 RMB',
    reason: '物料时长比贵阳售后库长'
  },
  {
    key: 'row-4',
    priority: 4,
    available: true,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '201A',
    storageName: '上海仓库',
    availableQuantity: '1000 EA',
    duration: '55 H',
    amount: '780 RMB',
    reason: '物流时长比北京售后库长'
  },
  {
    key: 'row-5',
    priority: 5,
    available: true,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '204D',
    storageName: '广州仓库',
    availableQuantity: '1600 EA',
    duration: '58 H',
    amount: '800 RMB',
    reason: '物流时长比上海售后库长'
  },
  {
    key: 'row-6',
    priority: 6,
    available: true,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '203C',
    storageName: '深圳仓库',
    availableQuantity: '1800 EA',
    duration: '58 H',
    amount: '850 RMB',
    reason: '运输时长比广州售后库长'
  },
  {
    key: 'row-7',
    priority: null,
    available: false,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '205E',
    storageName: '重庆仓库',
    availableQuantity: '800 EA',
    duration: '76 H',
    amount: '700 RMB',
    reason: '不符合整批调拨'
  },
  {
    key: 'row-8',
    priority: null,
    available: false,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '401F',
    storageName: '南京售后库',
    availableQuantity: '10 EA',
    duration: '54 H',
    amount: '750 RMB',
    reason: '不符合整批调拨'
  },
  {
    key: 'row-9',
    priority: null,
    available: false,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '403H',
    storageName: '洛阳售后库',
    availableQuantity: '12 EA',
    duration: '52 H',
    amount: '560 RMB',
    reason: '不符合整批调拨'
  },
  {
    key: 'row-10',
    priority: null,
    available: false,
    materialCode: '10000293',
    materialDescription: '离心泵叶轮组件',
    companyCode: '1000',
    companyName: 'BestRun CN',
    plantCode: '1010',
    plantName: 'Plant 1010',
    storageCode: '405J',
    storageName: '合肥售后库',
    availableQuantity: '8 EA',
    duration: '52 H',
    amount: '750 RMB',
    reason: '不符合整批调拨'
  }
])

const aiRuleDialogRef = ref(null)
const confirmDialogRef = ref(null)
const emailDialogRef = ref(null)
const toastRef = ref(null)

const toastMessage = ref('')
const selectedKeys = ref([])

const confirmForm = reactive({
  material: '',
  plant: '',
  targetWarehouse: '',
  sourceWarehouse: '',
  quantity: '',
  date: new Date().toISOString().split('T')[0]
})

const emailContent = ref('')
const transferOrderNumber = ref('4820000011')

const emailForm = reactive({
  sender: 'scm-notify@joule.com',
  to: 'warehouse.team@joule.com',
  cc: 'supply-chain@joule.com',
  subject: ''
})

const defaultRules = Object.freeze([
  '不能从目标仓库调拨',
  '优先从物流时长最短的仓库调拨',
  '相同时长情况下，优先从运费最少的仓库调拨'
])

const userPreferenceRules = ref([
  '优先从售后库调拨',
  '必须整批调拨，不允许从多个仓库分批调拨'
])

const isTableLoading = ref(true)
const isActionLoading = ref(false)
const actionLoadingMessage = ref('')

const selectedRows = computed(() =>
  aiRecommendations.value.filter((row) => selectedKeys.value.includes(row.key))
)

const wait = (ms = 900) => new Promise((resolve) => setTimeout(resolve, ms))

const runWithLoading = async (message, task, delay = 900) => {
  if (isActionLoading.value) {
    return
  }

  actionLoadingMessage.value = message
  isActionLoading.value = true

  try {
    await wait(delay)
    await task()
  } finally {
    actionLoadingMessage.value = ''
    isActionLoading.value = false
  }
}

const showToast = async (message) => {
  toastMessage.value = message
  await nextTick()
  const toast = toastRef.value
  if (!toast) {
    return
  }
  if (typeof toast.show === 'function') {
    toast.show()
  } else {
    toast.open = true
  }
}

const isRowSelected = (key) => selectedKeys.value.includes(key)

const toggleRowSelection = (key) => {
  if (isRowSelected(key)) {
    selectedKeys.value = selectedKeys.value.filter((selectedKey) => selectedKey !== key)
  } else {
    selectedKeys.value = [...selectedKeys.value, key]
  }
}

const onCheckboxChange = (key, event) => {
  event.stopPropagation()
  const shouldSelect = event.target?.checked
  if (shouldSelect) {
    if (!isRowSelected(key)) {
      selectedKeys.value = [...selectedKeys.value, key]
    }
  } else {
    selectedKeys.value = selectedKeys.value.filter((selectedKey) => selectedKey !== key)
  }
}

const onRowKeydown = (event, key) => {
  if (event.key === ' ' || event.key === 'Enter') {
    event.preventDefault()
    toggleRowSelection(key)
  }
}

const openDialog = (dialogRef) => {
  const dialog = dialogRef.value
  if (!dialog) {
    return null
  }

  if (typeof dialog.show === 'function') {
    dialog.show()
  } else {
    dialog.open = true
  }

  return dialog
}

const closeDialog = (dialogRef) => {
  const dialog = dialogRef.value
  if (!dialog) {
    return
  }

  if (typeof dialog.close === 'function') {
    dialog.close()
  } else {
    dialog.open = false
  }
}

const openAiRuleDialog = () => {
  return runWithLoading('AI Agent 正在同步规则...', () => {
    openDialog(aiRuleDialogRef)
  }, 850)
}

const onAiRuleAfterClose = () => {
  showToast('AI 规则已就绪')
}

const buildEmailTemplate = () => {
  const first = selectedRows.value[0]
  const quantityText = confirmForm.quantity || first?.availableQuantity || '20 EA'
  const sourceText =
    confirmForm.sourceWarehouse ||
    [first?.storageCode, first?.storageName].filter(Boolean).join(' ') ||
    '来源仓库'
  const rawTarget = confirmForm.targetWarehouse || `${filters.targetWarehouse} 沈阳售后库`
  const targetText = rawTarget.replace(/目标仓库$/u, '').trim() || '沈阳售后库'
  const materialCode = confirmForm.material || first?.materialCode || '10000293'
  const requiredDate = confirmForm.date || '2025-11-18'

  return `尊敬的供应链管理部 / 仓储管理同事：

您好！

鉴于${targetText}当前离心泵叶轮组件（物料编码：${materialCode}）库存已低于安全阈值，无法满足近期售后维修工单需求，现申请从${sourceText}调拨 ${quantityText} 该物料至${targetText}。请协助在 ${requiredDate} 前完成入库，系统已生成调拨单 ${transferOrderNumber.value}。

如需补充需求证明或在库存、物流安排方面有任何疑问，欢迎随时与我联系。

感谢支持，敬请回复确认。
`
}

const onTransferClick = () => {
  if (!selectedKeys.value.length) {
    showToast('请先选择要调拨的行')
    return
  }

  const first = selectedRows.value[0]
  confirmForm.material = first.materialCode
  confirmForm.plant = `${first.plantCode} ${first.plantName}`
  confirmForm.targetWarehouse = `${filters.targetWarehouse} 沈阳售后库`
  confirmForm.sourceWarehouse = `${first.storageCode} ${first.storageName}`
  confirmForm.quantity = first.availableQuantity
  confirmForm.date = '2025-11-18'

  return runWithLoading('AI Agent 正在生成调拨方案...', () => {
    openDialog(confirmDialogRef)
  }, 1000)
}

const onConfirmDialogApprove = () => {
  return runWithLoading('AI Agent 正在创建调拨单...', () => {
    closeConfirmDialog()
    emailForm.subject = `调拨单 ${transferOrderNumber.value} 通知：${confirmForm.material}`
    emailContent.value = buildEmailTemplate()
    showToast(`已创建调拨单${transferOrderNumber.value}`)
    openDialog(emailDialogRef)
  }, 1000)
}

const onEmailSend = () => {
  return runWithLoading('AI Agent 正在发送通知邮件...', () => {
    closeEmailDialog()
    showToast('调拨通知已发送，流程完成 ✅')
  }, 1100)
}

const onEmailCancel = () => {
  closeEmailDialog()
}

const priorityDisplay = (row) => {
  if (!row.available) {
    return { icon: 'status-error', label: null }
  }
  return {
    icon: null,
    label: row.priority ?? ''
  }
}

const handleFilterInput = (key) => (event) => {
  filters[key] = event.target.value
}

const handleConfirmInput = (key) => (event) => {
  confirmForm[key] = event.target.value
}

const onConfirmDateChange = (event) => {
  confirmForm.date = event.target.value
}

const onEmailInput = (event) => {
  emailContent.value = event.target.value
}

const handleEmailFormInput = (key) => (event) => {
  emailForm[key] = event.target.value
}

const onEditPreferences = () => {
  closeDialog(aiRuleDialogRef)
  showToast('请在设置中修改当前用户偏好')
}

const closeAiRuleDialog = () => {
  closeDialog(aiRuleDialogRef)
}

const closeConfirmDialog = () => {
  closeDialog(confirmDialogRef)
}

const closeEmailDialog = () => {
  closeDialog(emailDialogRef)
}

onMounted(() => {
  wait(1800).then(() => {
    isTableLoading.value = false
  })
})

const durationTooltipRef = ref(null)
const amountTooltipRef = ref(null)
const aiReasonTooltipRef = ref(null)

const showPopover = (popoverRef, target) => {
  const popover = popoverRef.value
  if (popover) {
    popover.opener = target
    popover.open = true
  }
}

const closePopover = (popoverRef) => {
  const popover = popoverRef.value
  if (popover) {
    popover.open = false
  }
}

const openDurationTooltip = (event) => {
  showPopover(durationTooltipRef, event.currentTarget)
}

const closeDurationTooltip = () => {
  closePopover(durationTooltipRef)
}

const openAmountTooltip = (event) => {
  showPopover(amountTooltipRef, event.currentTarget)
}

const closeAmountTooltip = () => {
  closePopover(amountTooltipRef)
}

const openAiReasonTooltip = (event) => {
  showPopover(aiReasonTooltipRef, event.currentTarget)
}

const closeAiReasonTooltip = () => {
  closePopover(aiReasonTooltipRef)
}
</script>

<template>
  <div class="page">
    <ui5-shellbar primary-title="智能调拨系统" secondary-title="Inventory Transfer Assistant">
      <img slot="logo" src="/sap-logo.svg" alt="SAP logo" class="sap-logo" />
      <ui5-avatar slot="profile" icon="employee"></ui5-avatar>
    </ui5-shellbar>

    <main class="content">
      <section class="filter-card">
        <div class="card-title">库存调拨推荐</div>
        <div class="filter-grid">
          <div class="field">
            <ui5-label for="material">物料号</ui5-label>
            <ui5-input
              id="material"
              :value="filters.material"
              @input="handleFilterInput('material')"
            />
          </div>
          <div class="field">
            <ui5-label for="plant">工厂</ui5-label>
            <ui5-input id="plant" :value="filters.plant" @input="handleFilterInput('plant')" />
          </div>
          <div class="field">
            <ui5-label for="target-warehouse">目标仓库</ui5-label>
            <ui5-input
              id="target-warehouse"
              :value="filters.targetWarehouse"
              @input="handleFilterInput('targetWarehouse')"
            />
          </div>
          <div class="field">
            <ui5-label for="target-quantity">目标数量</ui5-label>
            <ui5-input
              id="target-quantity"
              :value="filters.targetQuantity"
              @input="handleFilterInput('targetQuantity')"
            />
          </div>
        </div>
        <div class="actions">
          <ui5-button design="Emphasized" icon="create">查询</ui5-button>
          <ui5-button design="Positive" icon="workflow-tasks" @click="onTransferClick">调拨</ui5-button>
          <ui5-button class="ai-rule-button" design="Transparent" @click="openAiRuleDialog">
            AI Rule
          </ui5-button>
        </div>
      </section>

      <section class="table-card wide">
        <div class="table-scroll">
          <div v-if="isTableLoading" class="table-loading">
            <ui5-busy-indicator size="Large" active></ui5-busy-indicator>
            <p>AI Agent 正在动态计算中...</p>
          </div>
          <ui5-table v-else class="recommendation-table" mode="None">
            <ui5-table-header-row slot="headerRow">
              <ui5-table-header-cell width="68px" min-width="64px">选择</ui5-table-header-cell>
              <ui5-table-header-cell width="88px" min-width="82px">优先级</ui5-table-header-cell>
              <ui5-table-header-cell width="128px" min-width="112px">物料编码</ui5-table-header-cell>
              <ui5-table-header-cell width="130px" min-width="70px">物料描述</ui5-table-header-cell>
            <ui5-table-header-cell width="160px" min-width="140px">公司</ui5-table-header-cell>
            <ui5-table-header-cell width="130px" min-width="100px">工厂</ui5-table-header-cell>
            <ui5-table-header-cell width="130px" min-width="100px">仓库</ui5-table-header-cell>
            <ui5-table-header-cell width="100px" min-width="80px">可用数量</ui5-table-header-cell>
            <ui5-table-header-cell
              class="tooltip-cell"
              width="100px"
              min-width="80px"
            >
              <span
                class="header-label hover-label derived-label"
                tabindex="0"
                @mouseenter="openDurationTooltip"
                @mouseleave="closeDurationTooltip"
                @focusin="openDurationTooltip"
                @focusout="closeDurationTooltip"
                >运输时长</span
              >
            </ui5-table-header-cell>
            <ui5-table-header-cell
              class="tooltip-cell"
              width="100px"
              min-width="80px"
            >
              <span
                class="header-label hover-label derived-label"
                tabindex="0"
                @mouseenter="openAmountTooltip"
                @mouseleave="closeAmountTooltip"
                @focusin="openAmountTooltip"
                @focusout="closeAmountTooltip"
                >运输成本</span
              >
            </ui5-table-header-cell>
            <ui5-table-header-cell
              class="tooltip-cell"
              width="300px"
              min-width="300px"
            >
              <div
                class="ai-header hover-label derived-label"
                tabindex="0"
                @mouseenter="openAiReasonTooltip"
                @mouseleave="closeAiReasonTooltip"
                @focusin="openAiReasonTooltip"
                @focusout="closeAiReasonTooltip"
              >
                <ui5-icon name="lightbulb" class="ai-header-icon"></ui5-icon>
                <span>AI排序结果</span>
              </div>
            </ui5-table-header-cell>
          </ui5-table-header-row>

          <ui5-table-row
            v-for="row in aiRecommendations"
            :key="row.key"
            class="table-row"
            :class="{ selected: isRowSelected(row.key) }"
            :data-key="row.key"
            tabindex="0"
            @click="toggleRowSelection(row.key)"
            @keydown.space.prevent="onRowKeydown($event, row.key)"
            @keydown.enter.prevent="onRowKeydown($event, row.key)"
          >
            <ui5-table-cell>
              <div class="selection-cell">
                <ui5-checkbox
                  class="row-checkbox"
                  :checked="isRowSelected(row.key)"
                  @change="onCheckboxChange(row.key, $event)"
                ></ui5-checkbox>
              </div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="priority-cell">
                <template v-if="priorityDisplay(row).icon">
                  <ui5-icon :name="priorityDisplay(row).icon" class="status-icon danger" />
                </template>
                <template v-else>
                  <span class="priority-badge">{{ priorityDisplay(row).label }}</span>
                </template>
              </div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell cell-strong">{{ row.materialCode }}</div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell ellipsis">{{ row.materialDescription }}</div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell dual-line">
                <span class="code">{{ row.companyCode }}</span>
                <span class="name">{{ row.companyName }}</span>
              </div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell dual-line">
                <span class="code">{{ row.plantCode }}</span>
                <span class="name">{{ row.plantName }}</span>
              </div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell dual-line">
                <span class="code">{{ row.storageCode }}</span>
                <span class="name">{{ row.storageName }}</span>
              </div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell">{{ row.availableQuantity }}</div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell">{{ row.duration }}</div>
            </ui5-table-cell>
            <ui5-table-cell>
              <div class="cell">{{ row.amount }}</div>
            </ui5-table-cell>
            <ui5-table-cell class="ai-reason-cell">
              <div class="cell ai-text">{{ row.reason }}</div>
            </ui5-table-cell>
            </ui5-table-row>
          </ui5-table>
        </div>
      </section>
    </main>

    <ui5-dialog
      ref="aiRuleDialogRef"
      class="dialog"
      header-text="AI 调拨规则"
      @after-close="onAiRuleAfterClose"
    >
      <div class="dialog-content ai-rules">
        <section class="rule-section default-section">
          <div class="rule-header">默认规则</div>
          <p class="rule-caption">依据集团策略统一配置，暂不支持修改</p>
          <ul class="rule-list">
            <li v-for="rule in defaultRules" :key="rule">{{ rule }}</li>
          </ul>
        </section>
        <section class="rule-section editable-section">
          <div class="rule-header">用户偏好</div>
          <p class="rule-caption">当前偏好会叠加到默认规则上，可按需微调</p>
          <ul class="rule-list">
            <li v-for="rule in userPreferenceRules" :key="rule">{{ rule }}</li>
          </ul>
          <ui5-button class="rule-edit-button" design="Transparent" @click="onEditPreferences">编辑偏好</ui5-button>
          <p class="rule-hint">点击编辑可调整个人偏好</p>
        </section>
      </div>
      <div slot="footer" class="dialog-footer">
        <ui5-button design="Emphasized" @click="closeAiRuleDialog">完成</ui5-button>
      </div>
    </ui5-dialog>

    <ui5-dialog ref="confirmDialogRef" class="dialog" header-text="调拨确认">
      <div class="dialog-content form-grid">
        <div class="field">
          <ui5-label for="confirm-material">物料</ui5-label>
          <ui5-input
            id="confirm-material"
            :value="confirmForm.material"
            @input="handleConfirmInput('material')"
          />
        </div>
        <div class="field">
          <ui5-label for="confirm-plant">工厂</ui5-label>
          <ui5-input
            id="confirm-plant"
            :value="confirmForm.plant"
            @input="handleConfirmInput('plant')"
          />
        </div>
        <div class="field">
          <ui5-label for="confirm-target">目标仓库</ui5-label>
          <ui5-input
            id="confirm-target"
            :value="confirmForm.targetWarehouse"
            @input="handleConfirmInput('targetWarehouse')"
          />
        </div>
        <div class="field">
          <ui5-label for="confirm-source">发出仓库</ui5-label>
          <ui5-input
            id="confirm-source"
            :value="confirmForm.sourceWarehouse"
            @input="handleConfirmInput('sourceWarehouse')"
          />
        </div>
        <div class="field">
          <ui5-label for="confirm-qty">数量</ui5-label>
          <ui5-input
            id="confirm-qty"
            :value="confirmForm.quantity"
            @input="handleConfirmInput('quantity')"
          />
        </div>
        <div class="field">
          <ui5-label for="confirm-date">日期</ui5-label>
          <ui5-date-picker
            id="confirm-date"
            :value="confirmForm.date"
            @change="onConfirmDateChange"
          />
        </div>
      </div>
      <div slot="footer" class="dialog-footer">
        <ui5-button design="Transparent" @click="closeConfirmDialog">取消</ui5-button>
        <ui5-button design="Emphasized" @click="onConfirmDialogApprove">确认</ui5-button>
      </div>
    </ui5-dialog>

    <ui5-dialog ref="emailDialogRef" class="dialog" header-text="发送 Email 通知">
      <div class="dialog-content">
        <div class="email-info">已创建调拨单 {{ transferOrderNumber }}</div>
        <div class="email-meta">
          <div class="field">
            <ui5-label for="email-sender">发件人</ui5-label>
            <ui5-input
              id="email-sender"
              :value="emailForm.sender"
              @input="handleEmailFormInput('sender')"
            />
          </div>
          <div class="field">
            <ui5-label for="email-to">收件人</ui5-label>
            <ui5-input
              id="email-to"
              :value="emailForm.to"
              @input="handleEmailFormInput('to')"
            />
          </div>
          <div class="field">
            <ui5-label for="email-cc">抄送</ui5-label>
            <ui5-input
              id="email-cc"
              :value="emailForm.cc"
              @input="handleEmailFormInput('cc')"
            />
          </div>
          <div class="field">
            <ui5-label for="email-subject">主题</ui5-label>
            <ui5-input
              id="email-subject"
              :value="emailForm.subject"
              @input="handleEmailFormInput('subject')"
            />
          </div>
        </div>
        <ui5-textarea
          rows="10"
          growing
          growing-max-lines="15"
          :value="emailContent"
          @input="onEmailInput"
        />
      </div>
      <div slot="footer" class="dialog-footer">
        <ui5-button design="Transparent" @click="onEmailCancel">否</ui5-button>
        <ui5-button design="Positive" @click="onEmailSend">是</ui5-button>
      </div>
    </ui5-dialog>

    <ui5-toast ref="toastRef" placement="BottomCenter">{{ toastMessage }}</ui5-toast>
    <ui5-popover ref="durationTooltipRef" hide-arrow placement-type="Top" class="tooltip-popover">
      <div class="tooltip-content">
        物流时长<br />
        将发出仓库、目标仓库的物料地点，与物料重量结合，通过调用外部物流网站API，得到运输时长和运输费用。<br />
      </div>
    </ui5-popover>
    <ui5-popover ref="amountTooltipRef" hide-arrow placement-type="Top" class="tooltip-popover">
      <div class="tooltip-content">
        调拨成本<br />
        将发出仓库、目标仓库的物料地点，与物料重量结合，通过调用外部物流网站API，得到运输时长和运输费用。<br />
      </div>
    </ui5-popover>
    <ui5-popover ref="aiReasonTooltipRef" hide-arrow placement-type="Top" class="tooltip-popover">
      <div class="tooltip-content">
        AI 推荐结果<br />
        智能调拨 Agent 结合 S/4 实时库存、物流时长、调拨成本、用户偏好等多维数据，通过加权评分模型对可调拨仓库进行综合评估与排序，为决策者提供更优的调拨建议。
      </div>
    </ui5-popover>

    <div v-if="isActionLoading" class="action-loading">
      <div class="action-loading-box">
        <ui5-busy-indicator size="Large" active></ui5-busy-indicator>
        <p>{{ actionLoadingMessage }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background-color: #f5f6f7;
}

.sap-logo {
  height: 1.5rem;
}

.content {
  flex: 1;
  padding: 1.5rem 2rem 2.5rem;
  max-width: min(100vw - 60px, 1600px);
  width: 100%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.filter-card,
.table-card {
  background-color: #ffffff;
  border-radius: 0.75rem;
  box-shadow: 0 0.25rem 1.25rem rgba(0, 0, 0, 0.05);
  padding: 1.5rem;
}

.card-title {
  font-size: 1.25rem;
  font-weight: 600;
  margin-bottom: 1.25rem;
  color: #1d2d3a;
}

.filter-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 1rem 1.5rem;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.actions {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  margin-top: 1.5rem;
}

.table-card {
  padding: 0;
  overflow: visible;
}

.table-scroll {
  width: 100%;
  overflow-x: auto;
  overflow-y: visible;
  position: relative;
}

.table-loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.75rem;
  min-height: 360px;
  color: #0a6ed1;
  font-weight: 600;
  letter-spacing: 0.01em;
}

.table-loading p {
  margin: 0;
  font-size: 1.05rem;
}

.table-card.wide .recommendation-table {
  min-width: 1380px;
}

.selection-cell {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 2rem;
}

.ai-reason-cell {
  min-width: 500px;
}

.ai-text {
  white-space: normal;
  line-height: 1.5;
  word-break: break-word;
}

.ai-header {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  font-weight: 600;
  color: #324a5e;
}

.header-label {
  display: inline-flex;
  align-items: center;
  font-weight: 600;
  color: #324a5e;
  cursor: help;
}

.hover-label {
  cursor: help;
  outline: none;
}

.hover-label:focus-visible {
  box-shadow: 0 0 0 2px rgba(10, 110, 209, 0.2);
  border-radius: 0.4rem;
}

.derived-label {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.2rem 0.75rem;
  border-radius: 999px;
  background: linear-gradient(135deg, rgba(10, 110, 209, 0.12), rgba(10, 110, 209, 0.24));
  border: 1px solid rgba(10, 110, 209, 0.35);
  color: #0a6ed1;
  font-weight: 700;
  letter-spacing: 0.02em;
  box-shadow: 0 4px 12px rgba(10, 110, 209, 0.12);
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.derived-label:hover,
.derived-label:focus-visible {
  transform: translateY(-1px);
  box-shadow: 0 6px 16px rgba(10, 110, 209, 0.18);
}

:deep(ui5-table-header-row) {
  overflow: visible;
}

:deep(.tooltip-cell) {
  overflow: visible !important;
}

:deep(.tooltip-cell .hover-label:hover),
:deep(.tooltip-cell .hover-label:focus-visible) {
  z-index: 30;
  position: relative;
}

.tooltip-content {
  font-size: 0.8rem;
  line-height: 1.4;
  color: #1d2d3a;
  max-width: 260px;
  padding: 0.25rem 0.5rem;
}

:deep(.tooltip-popover::part(content)) {
  padding: 0.4rem 0.75rem;
}

.action-loading {
  position: fixed;
  inset: 0;
  background: rgba(255, 255, 255, 0.65);
  backdrop-filter: blur(2px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.action-loading-box {
  background: #ffffff;
  border-radius: 0.85rem;
  padding: 1.5rem 2.25rem;
  box-shadow: 0 1.5rem 3rem rgba(15, 55, 95, 0.16);
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: center;
  min-width: 260px;
  text-align: center;
  color: #0a2a43;
  font-weight: 600;
}

.action-loading-box p {
  margin: 0;
}

.ai-header-icon {
  color: #0a6ed1;
  font-size: 1rem;
}

:deep(.row-checkbox) {
  --sapField_BorderColor: #5d7ec5;
  --sapField_Hover_BorderColor: #0a6ed1;
  --sapField_Background: #ffffff;
}

:deep(.row-checkbox::part(root)) {
  padding: 0;
}

:deep(.table-row) {
  cursor: pointer;
  transition: background-color 0.12s ease;
}

.recommendation-table {
  width: 100%;
  --sapList_HeaderBorderColor: #d5deeb;
  --sapList_BorderColor: #e4e9f1;
  --sapList_Background: #ffffff;
  --sapList_Hover_Background: #f3f6f9;
  --sapList_SelectionBackgroundColor: #ebf3ff;
  --sapTable_Header_Row_Background: transparent;
}

:deep(.ui5-table-header-row) {
  background: linear-gradient(180deg, #f7f9fc 0%, #eef3f9 100%);
}

:deep(.ui5-table-header-row .ui5-table-cell) {
  border-bottom: 1px solid #d5deeb;
  font-size: 0.85rem;
  font-weight: 600;
  color: #324a5e;
  padding: 0.75rem 1rem;
}

:deep(.table-row .ui5-table-cell) {
  border-bottom: 1px solid #e4e9f1;
  padding: 0.85rem 1rem;
  font-size: 0.95rem;
  color: #2f3c48;
}

:deep(.table-row:nth-child(even) .ui5-table-cell) {
  background-color: #fafcff;
}

:deep(.table-row:not(.selected):hover .ui5-table-cell) {
  background-color: #f3f6f9;
}

:deep(.table-row.selected .ui5-table-cell) {
  background-color: #ebf3ff;
  box-shadow: inset 3px 0 0 #0a6ed1;
}

:deep(.table-row.selected:hover .ui5-table-cell) {
  background-color: #e1ecff;
}

:deep(.table-row .ui5-table-cell:not(:last-child)) {
  border-right: 1px solid #edf1f6;
}

.priority-cell {
  display: inline-flex;
  justify-content: center;
  align-items: center;
  min-width: 1.75rem;
}

.priority-badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 1.75rem;
  height: 1.75rem;
  border-radius: 0.4rem;
  background-color: #e5f5eb;
  color: #107e3e;
  font-weight: 600;
  font-size: 0.95rem;
}

.status-icon {
  font-size: 1.1rem;
  color: inherit;
}

.ai-rules {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 0.25rem 0 0.75rem;
}

.rule-section {
  border: 1px solid #d5deeb;
  border-radius: 0.6rem;
  padding: 1rem 1.25rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.rule-section.default-section {
  background-color: #f5f7fa;
  color: #4c6275;
}

.rule-section.editable-section {
  background-color: #ffffff;
}

.rule-header {
  font-weight: 600;
  color: #1d2d3a;
}

.rule-caption {
  margin: 0;
  color: #5f7387;
  font-size: 0.85rem;
}

.rule-list {
  margin: 0;
  padding-left: 1.2rem;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
  color: #2f3c48;
}

.rule-edit-button {
  align-self: flex-start;
  margin-top: 0.25rem;
}

.rule-hint {
  margin: 0;
  font-size: 0.8rem;
  color: #6b7c90;
}

:deep(.ai-rule-button::part(button)) {
  border: 1px solid #c7d3e3;
  border-radius: 0.5rem;
  padding: 0 1.2rem;
}

:deep(.ai-rule-button::part(button):hover) {
  border-color: #0a6ed1;
}

:deep(.rule-edit-button::part(button)) {
  border: 1px solid #c7d3e3;
  border-radius: 0.5rem;
  padding: 0 1rem;
  color: #0a6ed1;
}

:deep(.rule-edit-button::part(button):hover) {
  background-color: #eef4fb;
}

.status-icon.danger {
  color: #a1260d;
}

.cell {
  display: inline-flex;
  align-items: center;
  font-size: 0.95rem;
  color: #2f3c48;
}

.dual-line {
  display: inline-flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 0.15rem;
  line-height: 1.3;
  max-width: 160px;
}

.dual-line .code {
  font-weight: 600;
  color: #0e2a45;
}

.dual-line .name {
  font-size: 0.85rem;
  color: #3f5567;
  white-space: normal;
}

.cell-strong {
  font-weight: 600;
  color: #0e2a45;
}

.ellipsis {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.wrap-text {
  white-space: normal;
  line-height: 1.45;
}

.dialog {
  max-width: 520px;
}

.dialog-content {
  padding: 0.5rem 0;
}

.email-info {
  margin-bottom: 0.75rem;
  padding: 0.55rem 0.75rem;
  border-radius: 0.5rem;
  background: #eef4fb;
  color: #0a6ed1;
  font-size: 0.9rem;
  font-weight: 600;
}

.email-meta {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1rem 1.5rem;
  margin-bottom: 1rem;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem 1.5rem;
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
  gap: 0.75rem;
  padding: 1rem 1.5rem 3rem;
}

:deep(.dialog-footer ui5-button::part(button)) {
  padding: 0 1.25rem;
  min-width: 4.5rem;
}

@media (max-width: 900px) {
  .content {
    padding: 1rem;
  }

  .filter-card,
  .table-card {
    padding: 1rem;
  }

  .dialog {
    max-width: 90vw;
  }
}

@media (max-width: 1360px) {
  .recommendation-table {
    min-width: 1020px;
  }
}
</style>
