<template>
    <div> <!-- for some reason if you do not put an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="inside_page_header" v-if="pageBanner" v-bind:style="{ background: 'linear-gradient(0deg, rgba(0,0,0,0.2), rgba(0,0,0,0.2)), url(' + pageBanner.image_url + ') center center rgba(19, 110, 178)' }">
                    <div class="main_container position_relative">
                        <h2>Leasing</h2>
                    </div>
                </div>
                <div class="main_container">
                    <div class="row">
                        <div class="col-md-12">
                            <breadcrumb></breadcrumb>
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-md-12">
                            <div v-if="main" v-html="main.body"></div>
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-md-12">
                            <a v-if="leasingBooklet" :href="leasingBooklet" target="_blank">
        		                <!--<div class="animated_btn leasing_btn">-->
        		                <!--    Leasing Booklet-->
        		                <!--</div>    -->
        		            </a>    
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-md-12">
                            <div class="leasing_contact" v-if="leasingInfo" v-html="leasingInfo.body"></div>
                        </div>
                    </div>
                </div>
                <div class="location_image_container">
                    <div class="location_image" v-if="pageImages" v-for="item in pageImages">
                        <img :src="item.image_url" alt="" />   
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
var _extends=Object.assign||function(e){for(var a=1;a<arguments.length;a++){var t=arguments[a];for(var n in t)Object.prototype.hasOwnProperty.call(t,n)&&(e[n]=t[n])}return e};define(["Vue","vuex"],function(e,a){return e.component("location-component",{template:template,data:function(){return{dataLoaded:!1,pageBanner:null,main:null,leasingInfo:null,leasingBooklet:null,pageImages:null}},created:function(){var e=this;this.loadData().then(function(a){var t=e.findRepoByName("Leasing Banner").images;e.pageBanner=null!=t?t[0]:{image_url:"//codecloud.cdn.speedyrails.net/sites/5d8b7f8a6e6f647c040d0000/image/jpeg/1529532304000/insidebanner2.jpg"};var n=e.findRepoByName("Leasing Booklet");n&&(e.leasingBooklet=n.images[0].image_url);var r=e.findRepoByName("Leasing Images");r&&(e.pageImages=r.images),e.main=a[1].data,e.leasingInfo=a[1].data.subpages[0],e.dataLoaded=!0})},computed:_extends({},a.mapGetters(["property","repos","findRepoByName"])),methods:{loadData:function(){var e;return regeneratorRuntime.async(function(a){for(;;)switch(a.prev=a.next){case 0:return this.property.mm_host=this.property.mm_host.replace("http:",""),a.prev=1,a.next=4,regeneratorRuntime.awrap(Promise.all([this.$store.dispatch("getData","repos"),this.$store.dispatch("LOAD_PAGE_DATA",{url:this.property.mm_host+"/pages/pleasanthill-leasing.json"})]));case 4:return e=a.sent,a.abrupt("return",e);case 8:a.prev=8,a.t0=a["catch"](1),console.log("Error loading data: "+a.t0.message);case 11:case"end":return a.stop()}},null,this,[[1,8]])}}})});
</script>