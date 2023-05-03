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
                  <v-col cols="12" md="12" lg="12" v-if="show">
                    <h2>Emi: {{ Math.round(emi) }}</h2>
                    <h2>Total amount payable: {{ totalAmountPayable }}</h2>
                    <h2>Total interest payable: {{ totalInterestPayable }}</h2>
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
                  <!-- <v-col cols="12" md="12" lg="12" v-if="show">
                    <v-simple-table>
                      <template v-slot:default>
                        <thead>
                          <tr>
                            <th class="text-left">Year</th>
                            <th class="text-left">Total Payment</th>
                            <th class="text-left">Balance</th>
                            <th class="text-left">Loan Paid to Date</th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr
                            v-for="(item, index) in tenureInMonths"
                            :key="`row${index}`"
                          >
                            <td>
                              {{
                                index == 0
                                  ? months[now.getMonth()]
                                  : now.getMonth() + index >= 12
                                  ? months[(now.getMonth() + index) % 12]
                                  : months[now.getMonth() + index]
                              }}
                            </td>
                            <td>{{ Math.round(emi) }}</td>
                            <td>
                              {{ Math.round(totalAmountPayable - emi * index) }}
                            </td>
                            <td>
                              {{ (emi * index * 100) / totalAmountPayable }}
                            </td>
                          </tr>
                        </tbody>
                      </template>
                    </v-simple-table>
                  </v-col> -->
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
                          <td>
                            <v-icon small class="mr-2" @click="toggle"
                              >{{ isOpen ? "mdi-minus" : "mdi-plus" }} </v-icon
                            >{{ group }}
                          </td>
                          <td>{{ calculateAnnualPrincipal(group) }}</td>
                          <td>{{ calculateAnnualPayment(group) }}</td>
                          <td>{{ calculateTotalAnnualBalance(group) }}</td>
                          <td>{{ calculateTotalAnnualPercentage(group) }}</td>
                        </template>
                        <th v-else class="d-none"></th>
                      </template>
                      <template v-slot:[`item.principalAmount`]="{ item }">
                        {{ Math.round(item.principalAmount) }}
                      </template>
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
        {
          text: "principalAmount",
          align: "start",
          value: "principalAmount",
        },
        {
          text: "Total Payment",
          align: "start",
          value: "totalPayment",
        },
        {
          text: "Balance",
          align: "start",
          value: "balance",
        },
        {
          text: "Loan Paid to Date",
          align: "start",
          value: "loanPaidToDate",
        },
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
    tenureInMonths() {
      return this.tenure * 12;
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

        let rowObj = {
          groupBy: groupBy,
          year: year,
          totalPayment: Math.round(this.emi),
          balance: Math.round(
            Math.round(this.totalAmountPayable) - totalEmiPaid
          ),
          loanPaidToDate: (this.emi * (i + 1) * 100) / this.totalAmountPayable,
          principalAmount: principalAmount,
          principalBalance: Math.round(this.loanAmount - principalAmount),
        };
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
      return Math.round(totalAnnualPayment);
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
      return Math.round(totalAnnualPayment);
    },
    calculateTotalAnnualPercentage(groupBy) {
      let balancePrincipal =
        this.loanAmount - this.calculateTotalAnnualBalance(groupBy);
      return ((balancePrincipal * 100) / this.loanAmount).toFixed(2);
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