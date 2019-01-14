<template>
    <div> <!-- for some reason if you do not put an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="inside_page_header" v-bind:style="{ background: 'linear-gradient(0deg, rgba(0,0,0,0.2), rgba(0,0,0,0.2)), url(' + pageBanner.image_url + ') center center' }">
                    <div class="main_container position_relative">
                        <h2>Location</h2>
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
                            <!--<div v-if="main" v-html="main.body"></div>-->
                            <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur vitae metus tristique, pulvinar libero non, tincidunt neque. Nam non molestie elit. Curabitur mattis elementum eros, sit amet vulputate tellus tincidunt et. In vehicula odio vel ultricies tempus. Aliquam in turpis vulputate, ultricies justo a, tincidunt risus. </p>
                        </div>
                    </div>
                </div>
                <div class="main_container">
                    <div class="location_map">
                        <iframe :src="propertyAddress()" width="100%" height="490" frameborder="0" style="border:0" allowfullscreen></iframe>
                    </div>
                </div>
                <div class="location_map main_container">
                    <!--<google-map :property="property" :zoom="16"></google-map>-->
                    <iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3146.3920966730293!2d-122.0638271853221!3d37.944629810142935!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x8085614767c80795%3A0x7001749a8f991c5a!2s105+Crescent+Dr%2C+Pleasant+Hill%2C+CA+94523%2C+USA!5e0!3m2!1sen!2sca!4v1511207996780" height="490" frameborder="0" style="border:0" allowfullscreen></iframe>
                </div>
                <div class="main_container">
                    <div class="row">
                        <div class="col-md-6">
                            <div v-if="address" v-html="address.body"></div>
                        </div>
                        <div class="col-md-6">
                            <div v-if="directions" v-html="directions.body"></div>
                        </div>
                    </div>
                </div>
                <div class="location_image_container">
                    <div class="location_image" v-if="pageImages" v-for="item in pageImages">
                        <img :src="item.image_url" alt="item.id" class="img_max" />   
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
	define(["Vue", "vuex", "vue!google_map"], function(Vue, Vuex, google_map ) {
		return Vue.component("location-component", {
            template: template, // the variable template will be injected
            data: function () {
                return {
                    dataLoaded: false,
                    pageBanner: null,
                    main: null,
                    address: null,
                    directions: null,
                    pageImages: null
                }
            },
            created() {
                this.loadData().then(response => {
                    var repo = this.findRepoByName('Location Banner').images;
                    if(repo != null) {
                        this.pageBanner = repo[0];
                    } else {
                        this.pageBanner = {
                            "image_url": "//codecloud.cdn.speedyrails.net/sites/5b71ea7e6e6f645001070000/image/jpeg/1529532304000/insidebanner2.jpg"
                        }
                    }
                    
                    var temp_repo = this.findRepoByName('Location Images');
                    if(temp_repo) {
                        this.pageImages = temp_repo.images;
                    }
                    this.main = response[1].data;
                    this.address = response[1].data.subpages[0]
                    this.directions = response[1].data.subpages[1]
                    this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'repos',
                    'findRepoByName'
                ]),
                getPropertyAddress() {
                    return this.property.name + ' ' + this.property.address1 + ' ' + this.property.city + ' ' + this.property.country + ' ' +this.property.province_state + ' ' + this.property.province_state
                }
            },
            methods: {
                loadData: async function () {
                    this.property.mm_host = this.property.mm_host.replace("http:", "");
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "repos"), this.$store.dispatch('LOAD_PAGE_DATA', {url: this.property.mm_host + "/pages/pleasanthill-location.json"})]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                propertyAddress() {
                    var address = this.property.name + "+" + this.property.address1 + "+" + this.property.city + "+" + this.property.province_state + "+" + this.property.country + this.property.postal_code
                    console.log("address", address)
                    var key ="AIzaSyCukCjH3fsuDYBdI44hZKL43m60jEToJjY"
                    var src = "https://www.google.com/maps/embed/v1/place?q=" + address + "&key="+ key
                    return src
                }
            }
        });
	});
</script>