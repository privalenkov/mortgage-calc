<template>
  <v-container class="d-sm-flex">
    <v-dialog v-model="isAlert">
      <v-card>
        <v-card-text class="text-center"> Ошибок нет </v-card-text>
        <v-card-actions class="d-flex justify-end">
          <v-btn color="primary" @click="isAlert = false">Ок</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-col sm="6">
      <v-row class="fill-height">
        <v-col>
          <v-form ref="form" v-model="valid">
            <v-container
              fluid
              class="fill-height my-5 px-10 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-select
                v-model="selectedBank"
                @update:modelValue="clearSelectedRatesAndMortgage"
                :items="banksList"
                :rules="[(v) => !!v || 'Банк не выбран']"
                label="Банк"
                variant="solo"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-select
                v-model="selectedBankProgram"
                :return-object="true"
                item-title="programName"
                :items="bankPrograms[selectedBank]"
                variant="solo"
                :rules="[(v) => !!v || 'Ипотечная программа не выбрана']"
                label="Ипотечная программа"
                no-data-text="Пусто"
                required
                @update:modelValue="clearSelectedRates"
              ></v-select>

              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Выбрать ставку"
                no-data-text="Пусто"
                required
              ></v-select>
            </v-container>

            <v-container
              fluid
              class="fill-height my-5 px-10 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-text-field
                v-model="costObject"
                :rules="[(v) => !!v || 'Введите стоимость объекта']"
                variant="solo"
                label="Стоимость объекта"
                required
              ></v-text-field>

              <v-list-subheader class="font-weight-black"
                >Первоначальный взнос {{ downPaymentPerc }}%</v-list-subheader
              >
              <v-text-field
                v-model="downPayment"
                :rules="downPaymentRules"
                variant="solo"
                label="Первоначальный взнос"
                required
              ></v-text-field>

              <v-text-field
                v-model="mortgageTerm"
                :rules="mortgageTermRules"
                variant="solo"
                label="Срок ипотеки мес."
                required
              ></v-text-field>
            </v-container>

            <v-container
              fluid
              class="fill-height my-5 px-10 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-checkbox
                v-model="finishingCbox"
                label="Отделка"
                required
              ></v-checkbox>
              <v-text-field
                v-if="finishingCbox"
                :rules="nameRules"
                variant="solo"
                label="Площадь"
                required
              ></v-text-field>

              <v-text-field
                v-if="finishingCbox"
                v-show="finishingCbox"
                :rules="nameRules"
                variant="solo"
                label="Стоимость отделки"
                required
              ></v-text-field>
            </v-container>
          </v-form>
        </v-col>
      </v-row>
    </v-col>
    <v-col sm="6">
      <div style="top: 0" class="position-sticky rounded-lg bg-grey-lighten-3">
        <div class="my-5 px-10 py-10">
          <div class="text-red">Увеличить стоимость на 0%</div>

          <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
            >Стоимость объекта недвижимости с предоставлением
            субсидии</v-list-item-subtitle
          >
          <div class="text-h2" style="word-break: break-word">0</div>

          <v-list-item-subtitle class="mt-5 font-weight-black"
            >Ежемесячный платеж</v-list-item-subtitle
          >

          <div class="text-h2" style="word-break: break-word">
            {{ costMonthCalc }}
          </div>

          <v-btn
            color="success"
            block
            class="text-wrap my-4 mt-10"
            size="x-large"
            @click="validate"
          >
            Проверить результат
          </v-btn>

          <v-btn variant="outlined" class="my-4 w-100" size="x-large">
            Инструкция по работе
          </v-btn>
        </div>
      </div>
    </v-col>
  </v-container>
</template>

<script>
export default {
  name: "HomePage",
  data() {
    return {
      isAlert: false,
      valid: true,
      banksList: [
        "ПАО СБЕРБАНК",
        "АО АЛЬФА-БАНК",
        "БАНК ВТБ (ПАО)",
        "ПАО ПРОМСВЯЗЬБАНК",
      ],
      bankPrograms: {
        "ПАО СБЕРБАНК": [
          {
            bankName: "ПАО СБЕРБАНК",
            debtCommissionInterest: 0,
            debtInterestRate: 1.7,
            debtMinimalStartInterest: 15,
            discount: "3",
            interestRates: [
              {
                comission: 0,
                debtMaxSum: 6000000,
                firstPaymentMin: 15,
                months: "0-360",
                rate: 3,
                id: 0,
                get nameRate() {
                  return `Ставка: ${this.rate} Срок: ${this.months} мес.`;
                },
              },
              {
                comission: 0,
                debtMaxSum: 6000000,
                firstPaymentMin: 15,
                months: "12-24",
                rate: 1.7,
                id: 0,
                get nameRate() {
                  return `Ставка: ${this.rate} Срок: ${this.months} мес.`;
                },
              },
            ],
            programId: 0,
            programName: "Госпрограмма 2020",
          },
        ],
      },
      finishingCbox: false,
      selectedBank: null,
      selectedBankProgram: null,
      selectedRates: null,
      costObject: 0,
      downPayment: 0,
      downPaymentRules: [
        (v) => !!v || "Введите первоначальный взнос",
        (v) =>
          (v && (+this.downPayment * 100) / +this.costObject >= 15) ||
          "Первоначальный взнос должен быть не меньше 15%",
        (v) =>
          (v && +this.costObject >= +this.downPayment) ||
          "Первоначальный взнос не может быть больше стоимости объекта",
      ],
      mortgageTerm: 0,
      mortgageTermRules: [
        (v) => !!v || "Введите срок ипотеки",
        (v) => (v && !!this.selectedRates) || "Ставка не выбрана",
        (v) => v && this.isInRange(+this.mortgageTerm),
      ],
      costMonth: 0,
    };
  },
  computed: {
    getRates() {
      const program = this.bankPrograms[this.selectedBank];
      if (!program) return [];
      return this.bankPrograms[this.selectedBank][0].interestRates;
    },
    downPaymentPerc() {
      const result = ((this.downPayment * 100) / this.costObject).toFixed(2);
      if (isNaN(parseFloat(result)) || !isFinite(result) || result > 100)
        return 0;
      return result;
    },
    costMonthCalc() {
      if (!this.valid) return 0;
      const i = +this.selectedRates?.rate / (12 * 100);
      const n = +this.mortgageTerm;
      const diff = +this.costObject - +this.downPayment;
      if (!i || !n) return 0;

      const result = ((i * (1 + i) ** n) / ((1 + i) ** n - 1)) * diff || 0;

      if (result < 0) return 0;
      return Math.round(result);
    },
  },
  methods: {
    isInRange(num) {
      if (!this.selectedRates) return true;
      const range = this.selectedRates.months.split("-");
      if (num > +range[1])
        return "Уменьшите срок ипотеки или выберите другую ипотечную программу";
      else if (num < +range[0])
        return "Увеличьте срок ипотеки или выберите другую ипотечную программу";
      return true;
    },
    validate() {
      this.$refs.form.validate();
      if (this.valid) this.isAlert = true;
    },
    clearSelectedRatesAndMortgage() {
      this.selectedBankProgram = null;
      this.selectedRates = null;
    },
    clearSelectedRates() {
      this.selectedRates = null;
    },
  },
};
</script>
