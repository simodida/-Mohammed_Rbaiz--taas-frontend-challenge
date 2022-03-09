<template lang="pug">
    .wrapper#wrapper(class="bg-white p-4 rounded-3")
            //- Start Heading
            h2(class="mb-3") Currency Converter
            //- End Heading
            //- Start Search Bar
            .search-bar
                h5(class="mb-2") Enter Amount
                input(type="text", id="input", value="1", class="form-control", @keyup="getInputVal")
            //- End Search Bar
            //- Start Drop List
            .drop-list
                .list(class="d-flex align-items-center justify-content-between")
                    //- Start From Convert
                    .from
                        h5(class="mt-2 mb-1") From
                        .select-box(class="d-flex align-items-center")
                            img(:src="img_1 ? img_1 : 'https://flagcdn.com/48x36/us.png'", alt="all-country")
                            select(class="bg-white", @change="addFlagSelect_1" :value="targetSelect_1 ? targetSelect_1 : 'USD'")
                                option(v-for="(cut, country) in country_list", :value="country") {{ country }}
                    //- End From Convert
                    i.fa-solid.fa-right-left(class="mt-4", @click="tempCountry")
                    //- Start To Convert
                    .to
                        h5(class="mt-2 mb-1") To
                        .select-box(class="d-flex align-items-center")
                            img(:src="img_2 ? img_2 : 'https://flagcdn.com/48x36/ma.png'", alt="all-country")
                            select(class="bg-white",  @change="addFlagSelect_2", :value="targetSelect_2 ? targetSelect_2 : 'MAD'")
                                option(v-for="(cut, country) in country_list", :value="country") {{ country }}
                    //- End To Convert
            //- End Drop List
            p(class="getting-exchange-txt my-2 mx-auto") {{ setTotalCurrency ? setTotalCurrency : (getExchangeTxt ? getExchangeTxt : "Getting exchange rate...")}}
            //- Start if a negative number is entered
            //- p(class="wrong text-center my-3 mx-auto text-danger d-none", id="wrong") Please enter a positive number
            //- End if a negative number is entered
            button(class="btn rounded-3 p-3", @click.prevent="getExchange") Get Exchange Rate
</template>

<script lang="ts">
import Vue from "vue";

// Import Country
import { country_list } from "../Global/Typescript/country-list";
// Import Country


export default Vue.extend({
    name: "Convert",
    data() {
        return {
            country_list, // Import from country-list.ts, It contains country in Object
            country: "",
            targetSelect_1: "", // It Target select, first 
            targetSelect_2: "", // It Target select, second
            img_1: "", // Image from the conversion
            img_2: "", // Image to the conversion
            inputVal: "", // Set input value
            getExchangeTxt: "", // Get msg "Getting exchange rate..."
            setTotalCurrency: "", // Set total currency
            tempCase: false
        }
    },
    methods: {
        // Start from convert
        addFlagSelect_1(e: Event) {
            this.targetSelect_1 = (e.target as HTMLInputElement).value;
            for(this.country in this.country_list) {
                if(this.country == this.targetSelect_1) {
                    this.img_1 = `https://flagcdn.com/48x36/${this.country_list[this.country].toLowerCase()}.png`;
                }
            }
        },
        // End from convert
        // Start to convert
        addFlagSelect_2(e: Event) {
            this.targetSelect_2 = (e.target as HTMLInputElement).value;
            for(this.country in this.country_list) {
                if(this.country == this.targetSelect_2) {
                    this.img_2 = `https://flagcdn.com/48x36/${this.country_list[this.country].toLowerCase()}.png`;
                }
            }
        },
        // End to convert
        // Start Get input value
        getInputVal(e: Event) {
            this.inputVal = (e.target as HTMLInputElement).value;
            if(this.inputVal == "" || this.inputVal == "0") {
                (e.target as HTMLInputElement).value = "1";
            }
        },
        // End Get input value
        // Start Get exchange
        async getExchange() {
            // let filterInputVal = this.inputVal.replace(/[^0-9]/ig, "").trim(); // if this or parseFloat()
            let inputValue = this.inputVal.trim();
            if(this.inputVal == "" || this.inputVal == "0") {
                this.inputVal = "1";
                inputValue = "1";
            }
            if(this.inputVal > "0") {
                this.getExchangeTxt = "Getting exchange rate...";
                const fromTarget_1 = this.targetSelect_1 ? this.targetSelect_1 : "USD";
                const toTarget_2 = this.targetSelect_2 ? this.targetSelect_2 : "MAD";
                const response = await fetch(`https://v6.exchangerate-api.com/v6/f1ce4fa3c6ca6041bef41aa3/latest/${fromTarget_1}`);
                const result = await response.json();
                const getConversionRate = result.conversion_rates[toTarget_2];
                const totalCurrency = (parseFloat(inputValue) * getConversionRate).toFixed(2);
                this.setTotalCurrency = `${parseFloat(inputValue)} ${fromTarget_1} = ${totalCurrency} ${toTarget_2}`;
            }
            else {
                return;
            }
        },
        // End Get exchange
        // Start temp country
        tempCountry() {
            this.tempCase = true;
            if(this.tempCase) {
                let temp = this.targetSelect_1;
                this.targetSelect_1 = this.targetSelect_2;
                this.targetSelect_2 = temp;

                let tmpImg = this.img_1;
                this.img_1 = this.img_2;
                this.img_2 = tmpImg;

                this.getExchange();

                this.tempCase = false;
            }
        }
        // End temp country
    }
});
</script>

<style lang="scss" scoped>

@import "../Global/sass/main.scss";
// Start Reset Classes Bootstrap
@import "../Global/reset-bootstrap/reset-bootstrap.scss";
// End Reset Classes Bootstrap
.wrapper {
    box-shadow: 1px 3px 10px rgba($color: #000000, $alpha: .4);
    h2 {
        color: $main-color;
    }
    // All h5
    h5 {
        text-align: start;
    }
    // All h5
    .search-bar {
        h5 {
            font: {
                size: 14px;
                weight: 500;
            }
        }
        input {
            width: 100%;
            border: 1px solid $main-color;
        }
    }
    // .wrong {
    //     width: 200px;
    // }
    .list {
        h5 {
            font: {
                size: 14px;
                weight: 500;
            }
        }
        i {
            cursor: pointer;
            color: #999;
            transition: .3s ease;
            &:hover {
                color: #000;
            }
        }
        .select-box {
            border: 1px solid #999;
            padding: 5px;
            border-radius: 5px;
            img {
                width: 24px;
                margin-right: 6px;
            }
            select {
                border: none;
                cursor: pointer;
                font: {
                    size: 13px;
                };
                &:focus {
                    outline: 0;
                }
                &::-webkit-scrollbar {
                    width: 8px;
                }
                &::-webkit-scrollbar-track {
                    background-color: transparent;
                }
                &::-webkit-scrollbar-thumb {
                    background-color: #888;
                    border-radius: 8px;
                    border-right: 2px solid #fff;
                }
            }
        }
    }
    button {
        width: 100%;
        border: 0;
        background-color: $main-color;
        color: #fff;
        font-size: 14px;
        cursor: pointer;
        &:active {
            transform: scale(0.98);
        }
    }
}
</style>
