<template>
  <v-app>
    <v-main>
      <v-container fluid>
        <v-row>
          <v-col cols="12" md="12" lg="8" offset="2">
            <v-card outlined class="pa-5">
              <v-card-title>Loan Calculator </v-card-title>
              <v-card-text>
                <v-row>
                  <v-col cols="12" md="4" lg="4">
                    <v-text-field
                      label="Loan Amount"
                      hide-details
                      v-model.number="loanAmount"
                      type="number"
                      outlined
                      dense
                      prepend-icon="mdi-currency-inr"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="8" lg="8">
                    <v-slider
                      v-model="loanAmount"
                      :max="20000000"
                      :min="0"
                      track-color="grey"
                      track-fill-color="orange"
                      thumb-color="orange"
                    >
                    </v-slider>
                    <div class="steps ml-5">
                      <span
                        v-for="(step, index) in loanAmountSteps"
                        class="tick"
                        :key="`loanAmountTick${index}`"
                        :style="step.style"
                        >|<br /><span class="marker">{{
                          step.text
                        }}</span></span
                      >
                    </div>
                  </v-col>

                  <v-col cols="12" md="4" lg="4">
                    <v-text-field
                      hide-details
                      label="Interest Rate"
                      v-model.number="interestRate"
                      type="number"
                      outlined
                      dense
                      prepend-icon="mdi-percent-outline"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="8" lg="8">
                    <v-slider
                      v-model="interestRate"
                      :max="20"
                      :min="0"
                      track-color="grey"
                      track-fill-color="orange"
                      thumb-color="orange"
                    >
                    </v-slider>

                    <div class="steps ml-5">
                      <span
                        class="tick"
                        v-for="(step, index) in loanInterestSteps"
                        :style="step.style"
                        :key="`loanInterestStep${index}`"
                        >|<br /><span class="marker">{{
                          step.text
                        }}</span></span
                      >
                    </div>
                  </v-col>
                  <v-col cols="12" md="4" lg="4" class="d-flex justify-start">
                    <v-text-field
                      class="mr-1"
                      label="Loan Tenure"
                      v-model.number="tenure"
                      type="number"
                      outlined
                      dense
                      prepend-icon="mdi-timer-sand"
                    ></v-text-field>
                    <v-btn-toggle dense v-model="tenureType">
                      <v-btn> Yr </v-btn>
                      <v-btn> Mo </v-btn>
                    </v-btn-toggle>
                  </v-col>

                  <v-col cols="12" md="8" lg="8">
                    <v-slider
                      v-model="tenure"
                      step="0.5"
                      :max="tenureType == 0 ? 30 : 360"
                      :min="0"
                      track-color="grey"
                      track-fill-color="orange"
                      thumb-color="orange"
                    >
                    </v-slider>
                    <div class="steps ml-5">
                      <span
                        class="tick"
                        v-for="(step, index) in loanTermSteps"
                        :style="step.style"
                        :key="`loanTermStep${index}`"
                        >|<br /><span class="marker">{{
                          step.text
                        }}</span></span
                      >
                    </div>
                  </v-col>
                  <template v-if="show">
                    <v-col cols="12" lg="12">
                      <v-divider></v-divider>
                    </v-col>
                    <v-col cols="12" md="12" lg="4">
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
                    <v-col
                      cols="12"
                      md="12"
                      lg="8"
                      class="d-flex flex-column text-left align-start justify-center"
                    >
                      <h1 class="my-2">
                        Monthly EMI {{ formatCurrency(Math.round(emi)) }}
                      </h1>
                      <h3 class="my-2">
                        Total Amount Payable
                        {{ formatCurrency(totalAmountPayable) }}
                      </h3>
                      <h3 class="my-2">
                        Total Interest Payable
                        {{ formatCurrency(totalInterestPayable) }}
                      </h3>
                    </v-col>
                    <v-col cols="12" md="12" lg="12">
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
                                >{{
                                  isOpen ? "mdi-minus" : "mdi-plus"
                                }} </v-icon
                              ><span class="ml-2">{{ group }}</span>
                            </td>
                            <!-- <td>{{ calculateAnnualPrincipal(group) }}</td> -->
                            <td class="text-left">
                              {{ calculateAnnualPayment(group) }}
                            </td>
                            <td class="text-left">
                              {{
                                formatCurrency(
                                  calculateTotalAnnualBalance(group)
                                )
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
                  </template>
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
      loanAmountSteps: [
        { text: "0", style: "left:0%;" },
        { text: "25L", style: "left:12.5%;" },
        { text: "50L", style: "left:25%;" },
        { text: "75L", style: "left:37.5%;" },
        { text: "100L", style: "left:50%;" },
        { text: "125L", style: "left:62.5%;" },
        { text: "150L", style: "left:75%;" },
        { text: "175L", style: "left:87.5%;" },
        { text: "200L", style: "left:100%;" },
      ],
      loanInterestSteps: [
        { text: "0", style: "left:0%;" },
        { text: "5", style: "left:25%;" },
        { text: "10", style: "left:50%;" },
        { text: "15", style: "left:75%;" },
        { text: "20", style: "left:100%;" },
      ],
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
            backgroundColor: ["#88A726", "#EE8D2B"],
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
    loanTermSteps() {
      if (this.tenureType == 0)
        return [
          { text: "0", style: "left:0%;" },
          { text: "5", style: "left:16.67%;" },
          { text: "10", style: "left:33.33%;" },
          { text: "15", style: "left:50%;" },
          { text: "20", style: "left:66.66%;" },
          { text: "25", style: "left:83.33%;" },
          { text: "30", style: "left:100%;" },
        ];
      else
        return [
          { text: "0", style: "left:0%;" },
          { text: "60", style: "left:16.67%;" },
          { text: "120", style: "left:33.33%;" },
          { text: "180", style: "left:50%;" },
          { text: "240", style: "left:66.66%;" },
          { text: "300", style: "left:83.33%;" },
          { text: "360", style: "left:100%;" },
        ];
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
  line-height: 0px;
  margin-left: -24px;
  position: absolute;
  text-align: center;
  width: 28px;
}

.marker {
  border: 1px solid transparent;
  color: #333;
  font-size: 9px;
  height: 18px;
  line-height: 18px;
  text-align: center;
}

.steps {
  position: relative;
  margin-top: -0.75rem;
}
</style>