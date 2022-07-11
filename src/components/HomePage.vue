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
                :rules="[(v) => !!v || 'Кредитная организация не выбрана']"
                label="Выберите кредитную организацию"
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
                :rules="[(v) => !!v || 'Программа кредитования не выбрана']"
                label="Выберите программу кредитования"
                no-data-text="Пусто"
                required
                @update:modelValue="clearSelectedRates"
              ></v-select>
            </v-container>

            <v-container
              fluid
              class="fill-height my-5 px-10 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-text-field
                v-model="costObject"
                :rules="[(v) => !!v || 'Укажите стоимость объекта']"
                variant="solo"
                label="Стоимость объекта (квартиры - из шахматки)"
                required
              ></v-text-field>

              <!-- площадь -->
              <v-row>
                <v-col cols="8">
                  <v-text-field
                    v-model="costObject"
                    :rules="[(v) => !!v || 'Укажите площадь объекта']"
                    variant="solo"
                    label="Укажите площадь объекта (с коэфф)., кв.м."
                    required
                  ></v-text-field>
                </v-col>
                <v-col>
                  <v-text-field
                    v-model="costObject"
                    variant="solo"
                    label="Площадь терассы"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>

            <!-- отделка -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Укажите текущую отделку объекта (квартиры)"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Укажите новый тип отделки (или выберите нет)"
                no-data-text="Пусто"
                required
              ></v-select>
            </v-container>

            <!-- выберите размер скидки -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-list-item-subtitle class="mb-5 font-weight-black text-wrap"
                >Выберите размер скидки</v-list-item-subtitle
              >
              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Платная бронь"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-text-field
                v-model="costObject"
                :rules="[(v) => !!v || 'Укажите стоимость объекта']"
                variant="solo"
                label="UDS"
                required
              ></v-text-field>

              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Скидка за скорость"
                no-data-text="Пусто"
                required
              ></v-select>
            </v-container>

            <!-- первоначальный взнос  -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-list-subheader class="font-weight-black"
                >Минимальный размер ПВ {{ downPaymentPerc }}%</v-list-subheader
              >
              <v-text-field
                v-model="downPayment"
                :rules="downPaymentRules"
                variant="solo"
                label="% первоначального взноса"
                required
              ></v-text-field>
            </v-container>

            <!-- кредит -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-list-item-subtitle class="font-weight-black text-wrap"
                >Сумма первоначального взноса</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">0</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Сумма кредита</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">0</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Базовая ставка, % годовых</v-list-item-subtitle
              >
              <div class="text-h4 mb-5" style="word-break: break-word">0</div>
              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Срок кредита, месяцев"
                no-data-text="Пусто"
                required
              ></v-select>
            </v-container>

            <!-- срок субсидирования -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-5 rounded-lg bg-grey-lighten-3"
            >
              <v-list-subheader class="font-weight-black"
                >Уточнить</v-list-subheader
              >
              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Срок субсидирования, месяцев"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-select
                v-model="selectedRates"
                :return-object="true"
                item-title="nameRate"
                :items="getRates"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Итоговая ставка с учетом субсидирования, % годовых"
                no-data-text="Пусто"
                required
              ></v-select>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Процент удорожания</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">0</div>
            </v-container>

            <!--  -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-list-item-subtitle class="font-weight-black text-wrap"
                >Стоимость объекта недвижимости с учетом
                удорожания</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">0</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Уточнение суммы первоначального взноса</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">0</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Сумма кредита</v-list-item-subtitle
              >
              <div class="text-h4 mb-5" style="word-break: break-word">0</div>
            </v-container>

            <!-- отделка -->
            <v-container
              fluid
              class="fill-height mt-5 px-10 pt-10 rounded-lg bg-grey-lighten-3"
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
      <div style="top: 0" class="position-sticky">
        <div class="my-5 px-8 py-5 rounded-lg bg-grey-lighten-3">
          <v-list-item-title class="font-weight-black text-wrap"
            >Справочно</v-list-item-title
          >
          <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
            >Сумма удорожания</v-list-item-subtitle
          >
          <div class="text-h3" style="word-break: break-word">0</div>
        </div>
        <div class="my-5 px-8 py-7 rounded-lg bg-grey-lighten-3">
          <v-list-item-subtitle class="font-weight-black text-wrap"
            >Новая стоимость объекта с учетом отделки</v-list-item-subtitle
          >
          <div class="text-h3" style="word-break: break-word">0</div>

          <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
            >Итого стоимость объекта с учетом отделки и скидки</v-list-item-subtitle
          >
          <div class="text-h3" style="word-break: break-word">0</div>

          <v-list-item-subtitle class="mt-5 font-weight-black"
            >Стандартный ежемесячный платеж</v-list-item-subtitle
          >

          <div class="text-h3" style="word-break: break-word">
            {{ costMonthCalc }}
          </div>

          <v-col class="d-flex px-0 py-0" style="gap: 20px;">
            <v-col
              cols="8"
              class="px-0 py-0 d-flex flex-column justify-space-between"
            >
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Ежемесячный платеж на период
                субсидирования</v-list-item-subtitle
              >
              <div class="text-h3" style="word-break: break-word">0</div>
            </v-col>
            <v-col
              cols="4"
              class="px-0 py-0 d-flex flex-column justify-space-between"
            >
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Выгода в месяц</v-list-item-subtitle
              >
              <div class="text-h3" style="word-break: break-word">0</div>
            </v-col>
          </v-col>

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
      data: null,
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
  async mounted() {
    if (localStorage.data) {
      this.data = JSON.parse(localStorage.data);
    }
    const data = await fetch("http://localhost:4000/").then((res) =>
      res.json()
    );
    localStorage.data = JSON.stringify(data);
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
