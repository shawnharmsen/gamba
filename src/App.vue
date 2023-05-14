<template>
    <div id="app">
        <div class="form-container">
            <h2>Put addy here idiot</h2>
            <input type="text" v-model="address" placeholder="Enter Ethereum address">
            <button @click="submitForm">Submit</button>
        </div>
        <div v-if="errorMessage" class="error">
            <p>{{ errorMessage }}</p>
        </div>
        <div class="content" v-if="response">
            <div class="response">
                <pre>{{ prettyResponse }}</pre>
            </div>
            <div class="links">
                <div v-for="link in links" :key="link.name">
                    <span>{{ link.name }}: </span>
                    <a :href="link.url + address" target="_blank">{{ link.url + address }}</a>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            address: '',
            response: null,
            errorMessage: null,
            links: [
                { name: 'Etherscan', url: 'https://etherscan.io/address/' },
                { name: 'Bscscan', url: 'https://bscscan.com/address/' },
                { name: 'Polygonscan', url: 'https://polygonscan.com/address/' },
                { name: 'Snowtrace', url: 'https://snowtrace.io/address/' },
                { name: 'Arbiscan', url: 'https://arbiscan.io/address/' },
                { name: 'Optimistic Etherscan', url: 'https://optimistic.etherscan.io/address/' },
                { name: 'Arkham Intelligence', url: 'https://platform.arkhamintelligence.com/explorer/address/' },
                { name: 'Once Upon', url: 'https://www.onceupon.gg/finder/' },
                { name: 'Metasleuth', url: 'https://metasleuth.io/result/eth/' },
                { name: 'ChainEdge', url: 'https://app.chainedge.io/'}
            ]
        }
    },
    computed: {
        prettyResponse() {
            return JSON.stringify(this.response, null, 2);
        }
    },
    methods: {
        async submitForm() {
            if (!this.address || this.address.length !== 42 || !this.address.startsWith('0x')) {
                this.errorMessage = 'Please enter a valid Ethereum address.';
                return;
            }
            try {
                const res = await axios.get(`http://localhost:8080/v1/arkham/${this.address}`);
                this.response = res.data;
                this.errorMessage = null;
            } catch (err) {
                console.error(err);
                this.response = null;
                if (err.response) {
                    this.errorMessage = 'Error: ' + err.response.data;
                } else if (err.request) {
                    this.errorMessage = 'Error: No response received from server.';
                } else {
                    this.errorMessage = 'Error: ' + err.message;
                }
            }
        }
    }
}
</script>

<style>
body {
    background-color: #1a1a1a;
}

#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #bfbfbf;
    margin-top: 60px;
}

.form-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 20px;
}

.form-container input {
    width: 600px; /* Increase the width */
    font-size: 18px; /* Increase the font size */
}

.content {
    display: flex;
    justify-content: space-between;
}

.error {
    color: red;
    margin-top: 20px;
}

.response {
    margin-top: 20px;
    text-align: left;
    white-space: pre-wrap;
    background-color: #2c2c2c;
    padding: 10px;
    border-radius: 5px;
    flex: 1;
}

.links {
    margin-top: 20px;
    flex: 1;
    display: flex;
    flex-direction: column;
    padding-left: 20px;
}

.links div {
    margin-bottom: 10px;
}

.links a {
    color: #bfbfbf;
    margin-bottom: 10px;
}
</style>

