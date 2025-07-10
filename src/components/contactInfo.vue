<template>
<!--  手機浮動紐  -->
<!-- Mobile contact info -->
    <div class="mo-contact-info">
    <div class="button" 
      @click="modalOpen = true; modalType = 'phone'" v-if="info.phone">
      <img src="//h35.banner.tw/img/form/phone.svg" alt="撥打電話" srcset="" />
      <div>撥打電話</div>
    </div>
    <div class="button"
      @click="modalOpen = true; modalType = 'messenger'" v-if="info.fbMessage">
      <img src="//h35.banner.tw/img/form/messenger.svg" alt="FB 諮詢" srcset="" />
      <div>FB 諮詢</div>
    </div>
    <div class="button"
      @click="modalOpen = true; modalType = 'fb'" v-if="info.fbLink&&(!info.address||!info.phone)">
      <img src="//h35.banner.tw/img/form/fb.svg" alt="粉絲專頁" srcset="" />
      <div>粉絲專頁</div>
    </div>
    <a class="button" href="#order" > <!--  @click="scrollTo('.order')"> -->
      <img src="//h35.banner.tw/img/form/pen.svg" alt="即刻預約" srcset="" />
      <div>即刻預約</div>
    </a>
    <div class="button"
      @click="modalOpen = true; modalType = 'gmap'"  v-if="info.address" >
      <img src="//h35.banner.tw/img/form/gmap.svg" alt="地圖導航" srcset="" />
      <div>地圖導航</div>
    </div>
    <div class="button"
      @click="modalOpen = true; modalType = 'line'"  v-if="info.line" >
      <img src="//h35.banner.tw/img/form/line.svg" alt="Line" srcset="" />
      <div>Line</div>
    </div>
  </div>
<!-- 案扭區 -->
         <div class="contact-info">
         <div class="contact-button">
      <div class="button phone" @click="modalOpen=true; modalType='phone'" v-if="info.phone">
        <img src="//h35.banner.tw/img/form/phone.svg" alt="電話" srcset="" />
        <div>{{ info.phone }}</div>
      </div>
      <div class="button messenger" @click="modalOpen = true; modalType = 'messenger'" v-if="info.fbMessage">
        <img src="//h35.banner.tw/img/form/messenger.svg" alt="Facebook 諮詢" srcset="" />
        <div>Facebook 諮詢</div>
      </div>
      <div class="button fb" @click="modalOpen = true; modalType = 'fb'" v-if="info.fbLink">
      <!-- <div class="flex contact-item justify-center items-center btfanpage" @click="open()"> -->
        <img src="//h35.banner.tw/img/form/fb.svg" alt="前往粉絲專頁" srcset="" />
        <div>前往粉絲專頁</div>
      </div>
        </div>
    <div class="addr" v-if="info.address">
      <div class="address">
        <div><span v-if="info.address1">{{ info.address1 }}：</span>{{ info.address }}</div>
      </div>
      <div class="button googlemap"
        @click="modalOpen = true; modalType = 'gmap'">
        <img src="//h35.banner.tw/img/form/gmap.svg" alt="導航 GoogleMap" srcset="" />
        <div>導航 GoogleMap</div>
      </div>
    </div></div>
    <div class="google-map-container">
      <iframe
        class="google-map"
        :src="info.googleSrc"
        allowfullscreen=""
        loading="lazy"
        referrerpolicy="no-referrer-when-downgrade"
      ></iframe>
    </div>
        

        
  <!-- Modal -->
  <input type="checkbox" v-model="modalOpen" id="contact-modal" class="modal-toggle" />
  <div class="modal -mt-20 md:-mt-72">
    <div class="modal-box py-12 relative flex flex-col items-center justify-center">
      <label for="contact-modal" class="btn btn-sm btn-circle absolute right-4 top-4">✕</label>
      <!-- icon -->
      <img class="h-12" v-if="modalType == 'phone'" src="//h35.banner.tw/img/form/phone.svg" alt="phone" srcset="" />
      <img class="h-12" v-else-if="modalType == 'messenger'" src="//h35.banner.tw/img/form/messenger.svg" alt="messenger" srcset="" />
      <img class="h-12" v-else-if="modalType == 'fb'" src="//h35.banner.tw/img/form/fb.svg" alt="fb" srcset="" />
      <img class="h-12" v-else-if="modalType == 'gmap'" src="//h35.banner.tw/img/form/gmap.svg" alt="gmap" srcset="" />
      <img class="h-12" v-else-if="modalType == 'line'" src="//h35.banner.tw/img/form/line.svg" alt="line" srcset="" />
      <!-- title -->
      <div class="text-xl mt-4 font-bold">{{
       modalType == 'phone' ? '賞屋專線' :
       modalType == 'messenger' ? 'Facebook Messenger' :
       modalType == 'fb' ? 'Facebook 粉絲專頁' :
       modalType == 'line' ? 'LINE賞屋客服' :
       modalType == 'gmap' ? `${info.address2?info.address2:'導航地址'}` : 
       ''
      }}</div>
      <!-- content -->
      <div class="text-md mt-4">{{ 
      modalType == 'phone' ? info.phone : 
      modalType == 'messenger' ? '線上諮詢' : 
      modalType == 'fb' ? '' :
      modalType == 'line' ? '' :
      modalType == 'gmap' ? `${info.address}` : 
      ''
      }}</div>
      <!-- btn -->
      <div class="btn btn-lg bg-color1 border-0 text-white mt-12 hover:bg-color2" @click="go()" v-if="modalType != 'phone'" v-bind:class="{
        'hidden': modalType == 'phone' && !$isMobile(),
        'btlead': modalType == 'messenger',
        'btfanpage': modalType == 'fb',
        'btsearch': modalType == 'gmap',
        'btline': modalType == 'line',
        'btcontac': modalType == 'phone'
      }">
        {{ 
        modalType == 'phone' ? '撥打電話' : 
        modalType == 'messenger' ? '立即諮詢' : 
        modalType == 'fb' ? '前往粉絲專頁' :
        modalType == 'line' ? '加入' :
        modalType == 'gmap' ? '開啟導航' : 
        ''
        }}</div>
      <!-- btn phone -->
      <div class="btn btn-lg bg-color1 border-0 text-white mt-12 hover:bg-color2" @click="go()" id="phonegtm" v-else v-bind:class="{
        'hidden': modalType == 'phone',
        'btlead': modalType == 'messenger',
        'btfanpage': modalType == 'fb',
        'btsearch': modalType == 'gmap',
        'btline': modalType == 'line',
        'btcontac': modalType == 'phone'
      }">
        {{ modalType == 'phone' ? '撥打電話' : modalType == 'messenger' ? '立即諮詢' : modalType == 'fb' ? '前往粉絲專頁' :
        '開啟導航'
        }}</div>
    </div>
  </div>
</template>

<script setup>
import info from "@/info"
import { inject, ref } from "vue";
const modalOpen = ref(false);
const modalType = ref('');
const emit = defineEmits(['FormThanks'])

const go = () => {
  console.log('go triggered, modalType:', modalType.value);
  if (modalType.value == 'phone') {
    setTimeout(() => {
      emit('FormThanks','PhoneThanks')
    }, 1000);
   // window.location.href = `tel:${info.phone.replace("-", "")}`;
    window.open("https://hw-dm.com/dndl_tel_edm");
  } else if (modalType.value == 'messenger') {
    window.open(info.fbMessage);
  } else if (modalType.value == 'fb') {
    window.open(info.fbLink);
  } else if (modalType.value == 'gmap') {
    window.open(info.googleLink);
  } else if (modalType.value == 'line') {
    window.open(info.line);

  }
}

const smoothScroll = inject('smoothScroll')
const scrollTo = (el) => {
  smoothScroll({
    scrollTo: document.querySelector(el)
  })
}
</script>

<style scoped>

.bg-color1{background:#069;}
.hover\:bg-color2:hover{background-color:#047;}
/* 手機浮動紐 */
.mo-contact-info{  
  display: none;
  z-index: 99;
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100vw;
    height: calc(63 * 100vw / 375);
    gap: 1px;
    box-shadow: 0 0 calc(50 * 100vw / 375) #000c;
    font-size:calc(14 * 100vw / 375);
    background:#069;
    color: #fff;
}
.mo-contact-info .button{
  display: flex; flex-direction:column;justify-content:center;align-items:center;
  flex: 1;
  color:#fff ;
  text-decoration: none;
  border-left:1px solid #FFF6;
}
.mo-contact-info .button:first-child{
  border-left:0px solid #FFF6;
}
.mo-contact-info .button img{
        height: 1.14em;
        filter: brightness(0) invert(1);
}

/* 案扭區 */
.contact-info{
  width:calc(950 * 100vw / 1920);
  display: flex;
  flex-wrap:wrap;
  color: #666;gap: .8em;
  margin: 3vw auto 0;
}
.contact-button{width: 100%;
  display: flex;gap: .5em;
}
.contact-info .button{justify-content:center;align-items:center;
  border-radius:.5em;overflow:hidden;
  background:#ffe3bb;
  display: flex;transition:background .3s;
  cursor: pointer;
flex: 1;height: 3.5em;

}
.contact-info .button:hover{background:#ffefd9;}
.contact-info .button img{height: 1.4em;
  filter: brightness(0) invert(.4); 
  margin-right: 1.5em;}
.contact-info .addr{
  width: 100%;  
  display: flex;
  background: #fff;border-radius:.5em;overflow:hidden;
}
.contact-info .address{flex: 1;display: flex;justify-content:center;align-items:center;
}
.contact-info .googlemap {
  flex: 0 16.2vw;
}

.google-map-container {
  width: 100vw;
  height: 50vh;
  overflow: hidden;
  position: relative;
}

.google-map {
  border: 0;
  width: 100%;
  height: 100%;
}
/*  */
.modal-toggle{display: none;}
.modal{display: none;position: fixed;top: 0;left: 0;right: 0;bottom: 0;justify-content:center;align-items:center;margin: 0;background: #0003;backdrop-filter: blur(2px);
}
.modal-toggle:checked + .modal{display: flex;}


.modal-box {
  background: #fff;
  padding: 2em;
  border-radius: 1rem;
  width: 90%;
  max-width:450px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
  animation: fadeIn 0.3s ease-out;
  color: #666;
  text-align: center;
  position: relative;
}
.modal-box .btn {margin: 1em auto 0;}

#phonegtm{display: none;}

.modal-box img{
  margin: auto;
filter: invert(20%) sepia(36%) saturate(6641%) hue-rotate(184deg) brightness(94%) contrast(101%);
}
.modal-box .btn-sm{margin: 0em auto 0 0;position: absolute;top: 0;right: 0;}

@media screen and (max-width:768px) {
  
.mo-contact-info{  
  display: flex;
}
.contact-info{
font-size:calc(16 * 100vw / 375);
 width:calc(310 * 100vw / 375);
}
.contact-button{
  flex-direction:column;

}
.contact-info .button{
flex: 1  3.5em;height: 3.5em;

}
.contact-info .addr{
  flex-direction:column;
}
.contact-info .address{flex: 1  3.5em;height: 3.5em;
}
#phonegtm{display: block;}
}

</style>
