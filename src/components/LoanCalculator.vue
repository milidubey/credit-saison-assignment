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
                    <v-slider v-model="interestRate" :max="20" :min="0">
                    </v-slider>

                    <div class="steps" id="loanintereststeps">
                      <span class="tick" style="left: 0%"
                        >|<br /><span class="marker">0</span></span
                      ><span class="tick" style="left: 25%"
                        >|<br /><span class="marker">5</span></span
                      ><span class="tick" style="left: 50%"
                        >|<br /><span class="marker">10</span></span
                      ><span class="tick" style="left: 75%"
                        >|<br /><span class="marker">15</span></span
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
                    <v-btn-toggle v-model="tenureType">
                      <v-btn> Yr </v-btn>
                      <v-btn> Mo </v-btn>
                    </v-btn-toggle>
                  </v-col>
                  <v-col cols="12" md="8" lg="12">
                    <v-slider
                      v-model="tenure"
                      step="0.5"
                      :max="tenureType == 0 ? 30 : 360"
                      :min="0"
                      hide-details
                    >
                    </v-slider>
                    <div
                      class="steps"
                      id="loantermsteps"
                      v-if="tenureType == 0"
                    >
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
                    <div class="steps" id="loantermsteps" v-else>
                      <span class="tick" style="left: 0%"
                        >|<br /><span class="marker">0</span></span
                      ><span class="tick" style="left: 16.67%"
                        >|<br /><span class="marker">60</span></span
                      ><span class="tick" style="left: 33.33%"
                        >|<br /><span class="marker">120</span></span
                      ><span class="tick" style="left: 50%"
                        >|<br /><span class="marker">180</span></span
                      ><span class="tick" style="left: 66.67%"
                        >|<br /><span class="marker">240</span></span
                      ><span class="tick" style="left: 83.33%"
                        >|<br /><span class="marker">300</span></span
                      ><span class="tick" style="left: 100%"
                        >|<br /><span class="marker">360</span></span
                      >
                    </div>
                  </v-col>
                  <v-col cols="12" md="12" lg="12" v-if="show">
                    <h2>Emi: {{ formatCurrency(Math.round(emi)) }}</h2>
                    <h2>
                      Total amount payable:
                      {{ formatCurrency(totalAmountPayable) }}
                    </h2>
                    <h2>
                      Total interest payable:
                      {{ formatCurrency(totalInterestPayable) }}
                    </h2>
                  </v-col>
                  <v-col cols="12" md="12" lg="12" v-if="show">
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
                  <v-col cols="12" md="12" lg="12" v-if="show">
                    <v-data-table
                      group-by="groupBy"
                      :headers="headers"
                      :items="repaymentScheduleData"
                    >
                      <template
                        v-slot:group.header="{ group, items, isOpen, toggle }"
                      >
                        <template v-if="group != ''">
                          <td class="d-flex justify-start align-center">
                            <v-icon small class="mr-2" @click="toggle"
                              >{{ isOpen ? "mdi-minus" : "mdi-plus" }} </v-icon
                            ><span class="ml-2">{{ group }}</span>
                          </td>
                          <!-- <td>{{ calculateAnnualPrincipal(group) }}</td> -->
                          <td class="text-left">
                            {{ calculateAnnualPayment(group) }}
                          </td>
                          <td class="text-left">
                            {{
                              formatCurrency(calculateTotalAnnualBalance(group))
                            }}
                          </td>
                          <td class="text-left">
                            {{ calculateTotalAnnualPercentage(group) }}%
                          </td>
                        </template>
                        <th v-else class="d-none"></th>
                      </template>
                      <!-- <template v-slot:[`item.principalAmount`]="{ item }">
                        {{ Math.round(item.principalAmount) }}
                      </template> -->
                      <template v-slot:[`item.loanPaidToDate`]="{ item }"
                        >{{ item.loanPaidToDate.toFixed(2) }}%</template
                      >
                      <template v-slot:[`item.principalBalance`]="{ item }">{{
                        formatCurrency(item.principalBalance.toFixed(2))
                      }}</template>
                      <template v-slot:[`item.totalPayment`]="{ item }">{{
                        formatCurrency(item.totalPayment.toFixed(2))
                      }}</template>
                    </v-data-table>
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
import currency from "currency.js";

export default {
  data() {
    return {
      loanAmount: 0,
      interestRate: 0,
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
      now: new Date(),
      months: [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sept",
        "Oct",
        "Nov",
        "Dec",
      ],
      headers: [
        {
          text: "Year",
          align: "start",
          value: "year",
        },
        // {
        //   text: "principalAmount",
        //   align: "start",
        //   value: "principalAmount",
        // },
        {
          text: "Total Payment",
          align: "start",
          value: "totalPayment",
        },
        {
          text: "Balance",
          align: "start",
          value: "principalBalance",
        },
        {
          text: "Loan Paid to Date",
          align: "start",
          value: "loanPaidToDate",
        },
      ],
      tenureType: 0,
    };
  },
  components: {
    DoughnutChart,
  },
  computed: {
    emi() {
      // E = (p.r.(1 + r)^n) / ((1 + r)^n -1)
      let r = this.interestRate / 1200;
      let n = this.tenureInMonths;
      let emi =
        (this.loanAmount * r * Math.pow(1 + r, n)) / (Math.pow(1 + r, n) - 1);
      console.log("this.emi: ", emi);
      return emi;
    },
    totalAmountPayable() {
      return Math.round(this.emi * this.tenureInMonths);
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
    tenureInMonths() {
      if (this.tenureType == 0) return this.tenure * 12;
      else return this.tenure;
    },
    show() {
      if (this.interestRate && this.tenure && this.loanAmount > 0) return true;
      else return false;
    },
    repaymentScheduleData() {
      let data = [];

      //Calculate Data
      for (let i = 0; i < this.tenureInMonths; i++) {
        let year =
          i == 0
            ? this.months[this.now.getMonth()]
            : this.now.getMonth() + i >= 12
            ? this.months[(this.now.getMonth() + i) % 12]
            : this.months[this.now.getMonth() + i];

        let yearlyOffset = Math.trunc(Math.abs(this.now.getMonth() + i) / 12);

        let groupBy = new Date().getFullYear() + yearlyOffset;

        let totalEmiPaid = this.emi * (i + 1);

        let previousPrincipalAmount = 0;
        data.forEach((item) => {
          previousPrincipalAmount += item.principalAmount;
        });

        let interestAmount =
          i == 0
            ? this.loanAmount * (this.interestRate / 1200)
            : (this.loanAmount - previousPrincipalAmount) *
              (this.interestRate / 1200);

        let principalAmount = this.emi - interestAmount;
        let principalBalance = 0;
        if (i == 0) {
          principalBalance = Math.round(this.loanAmount - principalAmount);
        } else {
          principalBalance = Math.round(
            this.loanAmount -
              (data
                .map((item) => item.principalAmount)
                .reduce((a, b) => a + b, 0) +
                principalAmount)
          );
        }

        let rowObj = {
          groupBy: groupBy,
          year: year,
          totalPayment: Math.round(this.emi),
          balance: Math.round(
            Math.round(this.totalAmountPayable) - totalEmiPaid
          ),
          loanPaidToDate: (this.emi * (i + 1) * 100) / this.totalAmountPayable,
          principalAmount: principalAmount,
          principalBalance: principalBalance,
        };

        console.log("principalBalance ", rowObj.principalBalance);
        data.push(rowObj);
      }
      return data;
    },
  },
  methods: {
    calculateAnnualPayment(groupBy) {
      let totalAnnualPayment =
        this.repaymentScheduleData.filter((item) => item.groupBy == groupBy)
          .length * this.emi;
      return currency(Math.round(totalAnnualPayment), {
        symbol: "₹",
      }).format();
    },
    calculateTotalAnnualBalance(groupBy) {
      // let totalAnnualPrincipalBalance = 0;
      let totalPrincipal = 0;
      this.repaymentScheduleData.forEach((item) => {
        // console.log("item: ", item);
        if (item.groupBy <= groupBy) {
          // console.log("item.totalPrincipal: ", item.principalAmount);
          totalPrincipal += item.principalAmount;
          // console.log("totalPrincipal for " + groupBy + " : ", totalPrincipal);
        }
      });
      return Math.round(this.loanAmount - totalPrincipal);
    },
    calculateAnnualPrincipal(groupBy) {
      let totalAnnualPayment = 0;
      this.repaymentScheduleData.forEach((item) => {
        if (item.groupBy == groupBy) totalAnnualPayment += item.principalAmount;
      });
      return currency(totalAnnualPayment, {
        symbol: "₹",
        precision: 2,
      }).format();
    },
    calculateTotalAnnualPercentage(groupBy) {
      let balancePrincipal =
        this.loanAmount - this.calculateTotalAnnualBalance(groupBy);
      return ((balancePrincipal * 100) / this.loanAmount).toFixed(2);
    },
    formatCurrency(amount) {
      console.log("amount: ", amount);
      return currency(amount, {
        symbol: "₹",
      }).format();
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