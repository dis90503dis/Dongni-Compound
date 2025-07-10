<template>
  <div class="form-container order">
    <div class="form">
      <div class="left">
        <label class="row">
          <input type="text" placeholder="姓名" class="input" :value="formData.name" @input="(event) => (formData.name = event.target.value)" />
        </label>

        <label class="row">
          <input type="text" placeholder="手機" class="input" :value="formData.phone" @input="(event) => (formData.phone = event.target.value)" />
        </label>

        <label class="row">
          <select class="select" v-model="formData.city">
            <option value="" disabled>請選擇城市</option>
            <option v-for="city in cityList" :value="city.value" :key="city.value">
              {{ city.label }}
            </option>
          </select>
        </label>

        <label class="row">
          <select class="select" v-model="formData.area">
            <option value="" disabled>請選擇地區</option>
            <option v-for="area in areaList" :value="area.value" :key="area.value">
              {{ area.label }}
            </option>
          </select>
        </label>
      </div>

      <div class="right">
        <textarea class="textarea" placeholder="請輸入您的留言" :value="formData.msg" @input="(event) => (formData.msg = event.target.value)"></textarea>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, watch, inject } from "vue"
import { cityList, renderAreaList } from "../info/address.js"
import { useToast } from "vue-toastification"
const emit = defineEmits(['FormThanks'])

const toast = useToast()

const sending = ref(false)

const bypass = ["msg"] // 只有 msg 是非必填

const formDataRef = ref([
  "姓名", // name
  "手機", // phone
  // "房型", // room_type
  "居住縣市", // city
  "居住地區", // area
  "備註", // msg
])
const formData = reactive({
  name: "",
  phone: "",
 //  room_type: "",
  city: "",
  area: "",
  msg: "",
})
const roomTypeList = ref(["一房一廳", "兩房一廳", "三房兩廳", "四房以上"])
const areaList = ref([])
const recaptchaToken = inject("recaptchaToken")
const policyAgree = inject("policyAgree")
watch(
  () => formData.city,
  (newVal) => {
    areaList.value = renderAreaList(newVal)
    formData.area = areaList.value.length > 0 ? areaList.value[0].value : ""
  }
)

const send = () => {
  console.log("send() 被呼叫了") // ← 應該要看到
  const urlParams = new URLSearchParams(window.location.search)
  const utmSource = urlParams.get("utm_source")
  const utmMedium = urlParams.get("utm_medium")
  const utmContent = urlParams.get("utm_content")
  const utmCampaign = urlParams.get("utm_campaign")
  const time = new Date()
  const date = `${time.getFullYear()}-${time.getMonth() + 1}-${time.getDate()} ${time.getHours()}:${time.getMinutes()}:${time.getSeconds()}`
  const presend = new FormData()
  let pass = true
  let unfill = []
  let idx = 0
  if (!policyAgree.value) {
    toast.error("請先勾選『個資告知事項聲明』")
    return
  }
  if (!recaptchaToken.value) {
    toast.error("請完成『我不是機器人』驗證")
    return
  }
  for (const [key, value] of Object.entries(formData)) {
    if (!bypass.includes(key)) {
      if (value === "") {
        unfill.push(formDataRef.value[idx])
      }
    }
    idx++
    presend.append(key, value)
  }
  presend.append("g-recaptcha-response", recaptchaToken.value)
  presend.append("utm_source", utmSource)
  presend.append("utm_medium", utmMedium)
  presend.append("utm_content", utmContent)
  presend.append("utm_campaign", utmCampaign)
  presend.append("case_code", "f0c632c9-2185-4148-9801-e6322e173e5c")
  presend.append("message", formData.msg)
  console.log(Object.fromEntries(presend.entries()))
  if (unfill.length > 0) {
    pass = false
    toast.error(`「${unfill.join(", ")}」為必填或必選`)
    return
  }

  // 手機驗證
  const MobileReg = /^(09)[0-9]{8}$/
  if (!formData.phone.match(MobileReg)) {
    pass = false
    toast.error(`手機格式錯誤 (09開頭共10碼)`)
    return
  }

  if (pass && !sending.value) {
    sending.value = true

    fetch(
      `https://script.google.com/macros/s/AKfycbyQKCOhxPqCrLXWdxsAaAH06Zwz_p6mZ5swK80USQ/exec?name=${formData.name}
      &phone=${formData.phone}
      &msg=${formData.msg}
      &room_type=${formData.room_type}
      &cityarea=${formData.city}${formData.area}
      &utm_source=${utmSource}
      &utm_medium=${utmMedium}
      &utm_content=${utmContent}
      &utm_campaign=${utmCampaign}
      &date=${date}
      &campaign_name=東泥大院`,
      {
        method: "GET",
      }
    )

    fetch("https://service-sys.lixin.com.tw/reserve/f0c632c9-2185-4148-9801-e6322e173e5c", {
      method: "POST",
      body: presend,
    }).then((response) => {
      if (response.status === 200) {
        toast.success("資料送出成功！我們已收到您的預約")
        // emit('FormThanks','FormThanks')
        emit('FormThanks','FormThanks')
      }else if (response.status == 503 || response.status == 404 || response.status == 500) {
        emit('FormThanks','404')
      }
      formData.name = formData.phone = formData.msg = formData.city = formData.area = ""
      sending.value = false
      console.log(Object.fromEntries(presend.entries()))
      console.log("Send payload ⇒", Object.fromEntries(presend.entries()))
    })
  }
}
defineExpose({ send })
</script>

<style scoped>
.input::placeholder,
.textarea::placeholder {
  color: #000; /* 想要的顏色 */
  opacity: 1; /* Safari 預設會降到 0.54，手動拉回 */
}
.form-container {
  width: 100%;
  display: flex;
  justify-content: center;
}

.form {
  display: flex;
  flex-wrap: wrap;
  gap: 40px;
  max-width: 1000px;
  width: 100%;
  margin: 0px auto 0px auto;
  padding: 0px  20px  0px;
  box-sizing: border-box;
}

.left,
.right {
  flex: 1 1 45%;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.row {
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: 500;
    width: 100%;
    margin: auto;
}

.row span {
  width: 100px;
}

.required {
  color: red;
  font-size: 12px;
}

.input,
.select,
.textarea {
  flex: 1;
  padding: 1rem;
  border: 1px solid #ccc;
  font-size: 12px;
  box-sizing: border-box;
  font-family: "Noto Sans JP", sans-serif;
  background: rgba(255, 255, 255, 0.8);
  font-weight: 500;
  width: 100%;
}
.textarea {
  min-height: 120px;
  resize: none;
}
@media (max-width: 769px) {
  .form {
    flex-direction: column;
  }
  .left,
  .right {
    width: 100%;
    flex: none; /* 或 flex: 1 1 auto; 都可以 */
  }
  select{height: 45px;}
}
</style>
