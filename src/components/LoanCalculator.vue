<template>
  <v-app>
    <v-main>
      <v-container fluid>
        <v-row>
          <v-col cols="12" md="12" lg="9" offset="2">
            <v-card>
              <v-card-text>
                <v-row>
                  <v-col cols="12" md="12" lg="12">
                    <v-text-field
                      label="Loan Amount"
                      v-model.number="loanAmount"
                      type="number"
                      outlined
                      dense
                      append-icon="mdi-currency-inr"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="8" lg="12">
                    <v-slider v-model="loanAmount" :max="20000000" :min="0">
                    </v-slider>

                    <div class="steps" id="loanamountsteps">
                      <span class="tick" style="left: 0%"
                        >|<br /><span class="marker">0</span></span
                      ><span class="tick hidden-xs" style="left: 12.5%"
                        >|<br /><span class="marker">25L</span></span
                      ><span class="tick" style="left: 25%"
                        >|<br /><span class="marker">50L</span></span
                      ><span class="tick hidden-xs" style="left: 37.5%"
                        >|<br /><span class="marker">75L</span></span
                      ><span class="tick" style="left: 50%"
                        >|<br /><span class="marker">100L</span></span
                      ><span class="tick hidden-xs" style="left: 62.5%"
                        >|<br /><span class="marker">125L</span></span
                      ><span class="tick" style="left: 75%"
                        >|<br /><span class="marker">150L</span></span
                      ><span class="tick hidden-xs" style="left: 87.5%"
                        >|<br /><span class="marker">175L</span></span
                      ><span class="tick" style="left: 100%"
                        >|<br /><span class="marker">200L</span></span
                      >
                    </div>
                  </v-col>
                  <v-col cols="12" md="12" lg="12">
                    <v-text-field
                      label="Interest Rate"
                      v-model.number="interestRate"
                      type="number"
                      outlined
                      dense
                      append-icon="mdi-currency-inr"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="8" lg="12">
                    <v-slider
                      v-model="interestRate"
                      step="0.5"
                      :max="20"
                      :min="5"
                    >
                    </v-slider>

                    <div class="steps" id="loanintereststeps">
                      <span class="tick" style="left: 0%"
                        >|<br /><span class="marker">5</span></span
                      ><span class="tick" style="left: 16.67%"
                        >|<br /><span class="marker">7.5</span></span
                      ><span class="tick" style="left: 33.34%"
                        >|<br /><span class="marker">10</span></span
                      ><span class="tick" style="left: 50%"
                        >|<br /><span class="marker">12.5</span></span
                      ><span class="tick" style="left: 66.67%"
                        >|<br /><span class="marker">15</span></span
                      ><span class="tick" style="left: 83.34%"
                        >|<br /><span class="marker">17.5</span></span
                      ><span class="tick" style="left: 100%"
                        >|<br /><span class="marker">20</span></span
                      >
                    </div>
                  </v-col>
                  <v-col cols="12" md="12" lg="12">
                    <v-text-field
                      label="Loan Tenure"
                      v-model.number="tenure"
                      type="number"
                      outlined
                      dense
                      append-icon="mdi-currency-inr"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="8" lg="12">
                    <v-slider
                      v-model="tenure"
                      step="0.5"
                      :max="30"
                      :min="0"
                      hide-details
                    >
                    </v-slider>
                    <div class="steps" id="loantermsteps">
                      <span class="tick" style="left: 0%"
                        >|<br /><span class="marker">0</span></span
                      ><span class="tick" style="left: 16.67%"
                        >|<br /><span class="marker">5</span></span
                      ><span class="tick" style="left: 33.33%"
                        >|<br /><span class="marker">10</span></span
                      ><span class="tick" style="left: 50%"
                        >|<br /><span class="marker">15</span></span
                      ><span class="tick" style="left: 66.67%"
                        >|<br /><span class="marker">20</span></span
                      ><span class="tick" style="left: 83.33%"
                        >|<br /><span class="marker">25</span></span
                      ><span class="tick" style="left: 100%"
                        >|<br /><span class="marker">30</span></span
                      >
                    </div>
                  </v-col>
                  <v-col cols="12" md="12" lg="12">
                    <h2>Emi: {{ Math.round(emi) }}</h2>
                    <h2>Total amount payable: {{ totalAmountPayable }}</h2>
                    <h2>Total interest payable: {{ totalInterestPayable }}</h2>
                  </v-col>
                  <v-col cols="12" md="12" lg="12">
                    <div class="mb-5">
                      <DoughnutChart
                        id="amountPayableChart"
                        ref="amountPayableChart"
                        :chart-data="doughnutData"
                        :chart-options="doughtnutOptions"
                        chart-type="doughnut"
                      />
                    </div>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import DoughnutChart from "@/components/DoughnutChart";

export default {
  data() {
    return {
      loanAmount: 0,
      interestRate: 5,
      tenure: 0,
      doughtnutOptions: {
        legend: {
          display: true,
        },
        responsive: true,
        maintainAspectRatio: true,
        aspectRatio: 1.4,
        cutoutPercentage: 0,
        tooltips: {
          enabled: true,
        },
      },
    };
  },
  components: {
    DoughnutChart,
  },
  computed: {
    emi() {
      // E = (p.r.(1 + r)^n) / ((1 + r)^n -1)
      let r = this.interestRate / 1200;
      let n = this.tenure * 12;
      let emi =
        (this.loanAmount * r * Math.pow(1 + r, n)) / (Math.pow(1 + r, n) - 1);
      console.log("this.emi: ", emi);
      return emi;
    },
    totalAmountPayable() {
      return Math.round(this.emi * this.tenure * 12);
    },
    totalInterestPayable() {
      return this.totalAmountPayable - this.loanAmount;
    },
    doughnutData() {
      let data = {
        datasets: [
          {
            data: [this.loanAmount, this.totalInterestPayable],
            backgroundColor: ["#EE8D2B", "#88A726"],
            borderJoinStyle: "bevel",
          },
        ],
        labels: ["Principal Loan Amount", "Total Interest"],
      };
      return data;
    },
  },
};
</script>

<style>
.tick {
  color: #ccc;
  font-size: 11px;
  height: 11px;
  left: 0;
  line-height: 11px;
  margin-left: -24px;
  position: absolute;
  text-align: center;
  width: 30px;
}

.marker {
  border: 1px solid transparent;
  color: #333;
  font-size: 9px;
  height: 18px;
  line-height: 18px;
  text-align: center;
}
</style>