<template>
    <div class="table">

        <input class="table__mode-toggle_input" type="checkbox" id="mode-toggle" v-show="false" v-model="isModeWebsocket">
        <label class="table__mode-toggle_label" for="mode-toggle"></label>

        <div class="table__row table__row_header">
            <div class="table__cell table__cell_header" :class="{ 'table__cell_large': isModeWebsocket }">Name</div>
            <div class="table__cell table__cell_header" :class="{ 'table__cell_large': isModeWebsocket }">Price</div>
            <template v-if="!isModeWebsocket">
                <div class="table__cell table__cell_header">Market Cap</div>
                <div class="table__cell table__cell_header">Volume (24h)</div>
            </template>
        </div>
 
        <ul class="table__body">

            <template v-if="isModeWebsocket">
                <li class="table__row ws" v-for="(item, index) in websocketData" :key="index">
                    <div class="table__cell table__cell_body table__cell_large">{{ item.name }}</div>
                    <div class="table__cell table__cell_body table__cell_large">${{ shortenNumber(item.priceUsd) }}</div>
                </li>
            </template>

            <template v-else>
                <li class="table__row" v-for="(item, index) in staticData" :key="index">
                    <div class="table__cell table__cell_body">{{ item.name }}</div>
                    <div class="table__cell table__cell_body">${{ shortenNumber(item.priceUsd) }}</div>
                    <div class="table__cell table__cell_body">${{ shortenNumber(item.marketCapUsd) }}</div>
                    <div class="table__cell table__cell_body">${{ shortenNumber(item.volumeUsd24Hr) }}</div>
                </li>
            </template>
        </ul>
    </div>
</template>
 
<script>
export default {
    name: 'CurrencyTable',
    data() {
        return {
            isModeWebsocket: false,
            staticData: [],
            websocketData: [],
            websocket: null
        }
    },
    async created() {
        this.staticData = (await (await fetch('https://api.coincap.io/v2/assets?limit=15')).json()).data;
        this.websocketData = JSON.parse(JSON.stringify(this.staticData)); // JSON stuff to create a new copy


        this.websocket = new WebSocket('wss://ws.coincap.io/prices?assets=ALL');
        this.websocket.onmessage = msg => {
            const dataObj = JSON.parse(msg.data);
            
            Object.keys(dataObj).forEach(name => {
                const index = this.websocketData.map(value => value.id).findIndex(value => value === name);
                if (index !== -1) {
                    this.websocketData[index].priceUsd = dataObj[name];
                }
            })
        }
    },
    methods: {
        shortenNumber(number) {
            number = parseFloat(number).toFixed(8);
            const fractions = {
                1e+9: 'b',
                1e+6: 'm',
                1e+3: 'k'
            };

            for (const fraction of [1e+9, 1e+6, 1e+3]) {
                if (number > fraction) {
                    number /= fraction;
                    number = number.toFixed(2);
                    number += fractions[fraction];
                    break;
                }
            }

            return number;
        }
    }
}
</script>
 
<style lang="scss" scoped>
$color-primary-900: hsl(159, 70, 19);
$color-primary-700: hsl(159, 70, 39);
$color-primary-500: hsl(159, 70, 62);
$color-primary-300: hsl(159, 70, 77);
$color-primary-100: hsl(159, 70, 92);
 
.table {
    max-width: 100%;
    width: 60%;
    margin: 10vh auto 0;
    padding: 3rem;
    box-shadow: 0 0 5px hsla(0, 0%, 0%, 0.2);
    border-radius: 5px;

    &__body {
        list-style-type: none;
        padding: 0;
        max-height: 25vh;
        overflow: hidden;
        overflow-y: scroll;
        padding: 10px;
        padding-right: 5px;

        /**
            Due to Firefox having awful scrollbar styling support, we have to settle with the default
        */
        scrollbar-color: transparent transparent;

        &:hover {
            scrollbar-color: auto;
        }
        /**
            Firefox part ends here
            Chrome part starts here
        */
        &::-webkit-scrollbar {
            width: 5px;
        }
 
        &:hover::-webkit-scrollbar-thumb {
            background: rgba($color-primary-900, 0.3);
            border-radius: 5px;
        }
        /**
            Chrome part ends here
        */
    }
 
    &__row {
        display: flex;
        box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
        border-radius: 0.25rem;
 
        &_header {
            background: $color-primary-300;
        }
 
        &:not(&_header):hover {
            background: $color-primary-100;
        }
 
        &:not(:last-of-type) {
            margin-bottom: 1rem;
        }
    }
 
    &__cell {
        flex-basis: 25%;
        padding: 0.5rem;
 
        &_header {
            font-family: 'Raleway', sans-serif;
            font-weight: bold;
        }

        &_large {
            flex-basis: 50%;
        }
    }

    &__mode-toggle_label {
        display: block;
        left: 0;
        top: 0;
        margin-bottom: 2rem;
        position: relative;
        width: 15rem;
        height: 2rem;
        background: $color-primary-500;
        transition: all 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55);
        text-align: center;
        font-family: "Raleway", sans-serif;
        font-weight: bold;
        border-radius: 1rem;
        cursor: pointer;

        &::after {
            content: "Snapshot";
            width: 10rem;
            line-height: 2rem;
            position: absolute;
            left: 0;
            top: 0;
            display: block;
            background: $color-primary-900;
            transition: all 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55);
            border-radius: 1rem;
            color: white;
            cursor: pointer;
        }

        .table__mode-toggle_input:checked + & {
            left: 0;

            &::after {
                left: 5rem;
                background: $color-primary-700;
                content: "Websocket";
            }
        }
    }
}
 
@media (max-width: 767px) {
    .table {
        &__cell {
            flex-basis: 50%;
 
            &:nth-of-type(n+3) {
                display: none;
            }
        }
    }
}
</style>