<template>
    <div>
        <div :class="{ 'success-message': successMessage }">
            {{ successMessage }}
        </div>
        <h1>Create Deal and Account in Zoho CRM</h1>
        <form @submit.prevent="createRecord" class="form-container">


            <label for="dealName">Deal Name:</label>
            <span class="error-message" v-if="errors.dealName">{{ errors.dealName.join(', ') }}</span>
            <input type="text" id="dealName" v-model="dealName" required>

            <label for="dealStage">Deal Stage:</label>
            <span class="error-message" v-if="errors.dealStage">{{ errors.dealStage.join(', ') }}</span>
            <select id="dealStage" v-model="dealStage" required>
                <option value="Prospecting">Prospecting</option>
                <option value="Qualification">Qualification</option>
            </select>

            <label for="closingDate">Closing Date</label>
            <span class="error-message" v-if="errors.closingDate">{{ errors.closingDate.join(', ') }}</span>
            <input type="date" id="closingDate" v-model="closingDate" required>

            <label for="accountName">Account Name:</label>
            <span class="error-message" v-if="errors.accountName">{{ errors.accountName.join(', ') }}</span>
            <input type="text" id="accountName" v-model="accountName" required>

            <label for="accountWebsite">Account Website:</label>
            <span class="error-message" v-if="errors.accountWebsite">{{ errors.accountWebsite.join(', ') }}</span>
            <input type="text" id="accountWebsite" v-model="accountWebsite" required>

            <label for="accountPhone">Account Phone:</label>
            <span class="error-message" v-if="errors.accountPhone">{{ errors.accountPhone.join(', ') }}</span>
            <input type="text" id="accountPhone" v-model="accountPhone" required>

            <button type="submit">Create Record</button>
        </form>
    </div>
</template>
<script>
import axios from 'axios';
export default {

    props: {
        accessData: {
            type: Object,
            required: true
        }
    },
    data() {
        return {
            dealName: '',
            dealStage: 'Prospecting',
            accountName: '',
            accountWebsite: '',
            accountPhone: '',
            closingDate: '',


            successMessage: '',

            errors: {
                dealName: [],
                dealStage: [],
                closingDate: [],
                accountName: [],
                accountWebsite: [],
                accountPhone: [],
            },
        };
    },
    methods: {
        async refreshToken() {
            try {
                const response = await axios.post('http://127.0.0.1:8000/refresh-token', {
                    refreshToken: this.accessData.refresh_token
                });

                if (response.data.access_token) {
                    this.accessData.access_token = response.data.access_token;
                    this.accessData.expires_in = response.data.expires_in;
                    if (response.data.refresh_token) {
                        this.accessData.refresh_token = response.data.refresh_token;
                    }
                }
            } catch (error) {
                console.error('Error refreshing token:', error);
            }
        },

        createRecord() {

            this.successMessage = '';

            const recordData = {
                dealName: this.dealName,
                dealStage: this.dealStage,
                accountName: this.accountName,
                accountWebsite: this.accountWebsite,
                accountPhone: this.accountPhone,
                closingDate: this.closingDate,
                accessToken: this.accessData.access_token
            };

            axios.post('http://127.0.0.1:8000/create-deal', recordData)
                .then(response => {

                    this.dealName = '';
                    this.dealStage = 'Prospecting';
                    this.accountName = '';
                    this.accountWebsite = '';
                    this.accountPhone = '';
                    this.closingDate = '';

                    console.log('Record created successfully:', response.data.success);
                    this.refreshToken()
                    this.successMessage = response.data.success;
                })
                .catch(error => {
                    if (error.response && error.response.status === 422) {
                        this.errors = error.response.data.errors;
                        console.error('Validation errors:', error.response.data.errors);
                    } else {
                        console.error('Error creating record:', error);
                    }
                });
        }
    },


};
</script>


<style scoped>
.form-container {
    max-width: 600px;
    margin: 0 auto;
}

.form-group {
    margin-bottom: 20px;
}

label {
    display: block;
    margin-bottom: 5px;
}

input,
select {
    width: 100%;
    padding: 8px;
    box-sizing: border-box;
}

button {
    margin-top: 10px;
    padding: 10px;
    background-color: #007bff;
    color: #fff;
    border: none;
    cursor: pointer;
}

.success-message {
    color: green;
    font-weight: bold;
}

.error-message {
    color: red;
}
</style>