<template>
  <div class="form-container">
    <div>
      <form class="form-group" @submit.prevent="submitDonation">
        <label class="donate-label">Donate</label>
        <div class="form-check">
          <input id="ideal" v-model="method" class="method-radio" type="radio" value="ideal">
          <label class="method-label" for="ideal">
            <i class="pf pf-ideal"/>
            <span class="method-text-span">Ideal</span>
          </label>
          <input
            id="creditcard"
            v-model="method"
            class="method-radio"
            type="radio"
            value="creditcard"
          >
          <label class="method-label" for="creditcard">
            <i class="pf pf-credit-card"/>
            <span class="method-text-span">Creditcard</span>
          </label>

          <input id="paypal" v-model="method" class="method-radio" type="radio" value="paypal">
          <label class="method-label" for="paypal">
            <img class="paypal-icon" src="/icons/paypal.svg" alt="paypal icon">
            <span class="method-text-span">PayPal</span>
          </label>
        </div>
        <div class="input-container">
          <label for="amount" class="amount-label">€</label>
          <input
            id="amount"
            v-model="amount"
            class="amount-input"
            type="number"
            name="amount"
            min="1"
            placeholder="Amount"
            required
          >
        </div>
        <input
          id="description"
          v-model="description"
          class="description-input"
          type="text"
          name="description"
          placeholder="Message"
          autocomplete="off"
        >
        <button class="submit-button" type="submit" name="submit">Donate</button>
      </form>
    </div>
    <div v-if="donated" class="blur-screen">
      <div v-if="donated" class="donated-ok">
        <p>Thanks for your donation!</p>
        <button @click="donated = false">Close</button>
      </div>
    </div>
  </div>
</template>

<script>
import "~/assets/css/css/paymentfont.min.css";
const lambdaUrl = process.env.lambdaUrl || "http://localhost:3005/";
const URL_DONATION_SUBMIT = `${lambdaUrl}donate`;
const URL_DONATION_STATUS = `${lambdaUrl}donation/status`;

export default {
    data: function() {
        return {
            donated: false,
            method: "",
            amount: "",
            description: ""
        };
    },
    mounted() {
        this.paymentStatus();
    },
    methods: {
        submitDonation() {
            fetch(URL_DONATION_SUBMIT, {
                method: "POST",
                headers: {
                    "Content-Type": "application/json"
                },
                body: JSON.stringify({
                    method: this.method,
                    amount: this.amount,
                    description: this.description || "No message"
                })
            })
                .then(res => res.clone().json())
                .then(json => {
                    window.location = json.paymentURL;
                })
                .catch(error => console.log(`Error: ${error}`));
        },

        paymentStatus() {
            const orderId = this.$route.query.orderid;
            if (orderId !== undefined) {
                const url = URL_DONATION_STATUS.concat("/?orderid=", orderId);

                fetch(url)
                    .then(response => response.json())
                    .then(json => {
                        if (json.message === "Donation went ok") {
                            this.donated = true;

                            return;
                        }
                    })
                    .catch(err => console.log(err));
            }
        }
    }
};
</script>


<style lang="scss" scoped>
* {
    box-sizing: border-box;
}
.form-container {
    width: 310px;
    position: relative;
    height: 340px;
    margin: 0 10px 0 10px;
    border-radius: 6px;

    .donate-label {
        margin-bottom: 20px;
        padding: 0;
        position: relative;
        width: calc(90% - 5px);
        font-weight: bold;
        font-size: 30px;
    }

    .form-check {
        display: flex;
        flex-wrap: nowrap;
        flex-direction: column;
        justify-content: center;
        position: relative;
        padding: 10px;
        .method-radio {
            position: relative;
            width: auto;
            top: 0.72em;
            zoom: 2;
            padding-right: 5px;
            margin-right: 0;
            padding: 0;
            vertical-align: middle;
            cursor: pointer;
        }
        .method-label {
            display: flex;
            font-size: 25px;
            overflow: visible;
            position: relative;
            font-style: normal;
            cursor: pointer;
            margin-left: 25px;
            padding: 0;
            width: 50px;
            color: $color-purple;

            .paypal-icon {
                width: 100%;
                height: 30px;
            }
        }
    }
    .input-container {
        margin-top: 20px;
        height: 50px;
        border-radius: 5px;
        display: flex;
        justify-content: flex-start;
        align-items: center;
        background-color: rgb(219, 213, 213);
        .amount-label {
            display: block;
            flex: 0 1 40px;
            text-align: center;
            height: 100%;
            position: relative;
            left: 0;
            top: 0;
            line-height: 45px;
            border: 1px solid rgb(219, 213, 213);
            background-color: rgb(219, 213, 213);
            border-radius: 5px;
            color: $color-purple;

            font-size: 25px;
            padding: 0 5px;
            margin: 0;
        }
        .amount-input {
            flex: 1 1 auto;
            height: 100%;
            margin-left: 3px;
            border: 2px solid rgb(219, 213, 213);
            background-color: rgb(255, 255, 255);
            border-radius: 5px;
            font-size: 25px;
            padding: 5px;
        }
    }

    .form-group {
        text-align: center;
        .description-input {
            width: 100%;
            margin-top: 10px;
            border: 2px solid rgb(219, 213, 213);
            background-color: rgb(255, 255, 255);
            border-radius: 5px;
            font-size: 20px;
            padding: 5px 5px;
        }
        .submit-button {
            margin-top: 15px;
            border-radius: 6px;
            background-color: white;
            color: $color-purple;
            cursor: pointer;
        }
    }
    .blur-screen {
        position: absolute;
        left: 0;
        right: 0;
        bottom: 0;
        top: 0;
        width: 100%;
        height: 100%;
        // background: rgba(0, 0, 0, 0.2);
    }
    .donated-ok {
        display: flex;
        flex-direction: column;
        position: absolute;
        top: 10px;
        left: 10px;
        font-size: 20px;
        width: 330px;
        height: 150px;
        border: 2px solid rgb(219, 213, 213);
        background-color: rgb(255, 255, 255);
        border-radius: 5px;
        box-shadow: 0px 0px 11px 0px rgba(0, 0, 0, 0.21);
        animation: fadeIn 0.5s;

        p {
            text-align: center;
            padding: 50px 5px 5px 5px;
        }

        button {
            margin: auto;
            border: 2px solid rgb(219, 213, 213);
            background-color: rgb(255, 255, 255);
            border-radius: 5px;
            padding: 5px;
        }
    }

    @keyframes fadeIn {
        from {
            opacity: 0;
        }
        to {
            opacity: 1;
        }
    }
}

/* desktop */
@media screen and (min-width: 340px) {
    .form-container {
        width: 100%;
    }
}

/* desktop */
@media screen and (min-width: 767px) {
    .form-container {
        width: 50%;
        .form-check {
            .method-text-span {
                display: inline-block;
            }
        }

        .donated-ok {
            top: 100px;
            left: 140px;
        }
    }
}
</style>
