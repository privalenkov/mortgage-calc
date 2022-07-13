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
                v-model="selectedCity"
                :items="['Киров', 'Ижевск', 'Ульяновск']"
                :rules="[(v) => !!v || 'Кредитная организация не выбрана']"
                label="Укажите город, где планируется сделка"
                variant="solo"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-select
                v-model="selectedBank"
                :items="getBanks"
                :rules="[(v) => !!v || 'Кредитная организация не выбрана']"
                label="Выберите кредитную организацию"
                variant="solo"
                no-data-text="Пусто"
                required
                @update:modelValue="clearSelectedCreditOrgan"
              ></v-select>

              <v-select
                v-model="selectedBankProgram"
                :items="getBankPrograms"
                variant="solo"
                :rules="[(v) => !!v || 'Программа кредитования не выбрана']"
                label="Выберите программу кредитования"
                no-data-text="Пусто"
                required
                @update:modelValue="clearSelectedCreditProgram"
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
                    v-model="objectArea"
                    :rules="[(v) => !!v || 'Укажите площадь объекта']"
                    variant="solo"
                    label="Укажите площадь объекта (с коэфф)., кв.м."
                    required
                  ></v-text-field>
                </v-col>
                <v-col>
                  <v-text-field
                    v-model="terraceArea"
                    variant="solo"
                    label="Площадь терассы"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>

              <v-select
                v-model="selectedFormatObject"
                :items="[
                  'Студия',
                  'Однокомнатная',
                  'Евро двухкомнатная',
                  'Двухкомнатная',
                  'Евро трехкомнатная',
                  'Трехкомнатная',
                  'Евро четырехкомнатная',
                  'Четырехкомнатная',
                ]"
                variant="solo"
                :rules="[(v) => !!v || 'Программа кредитования не выбрана']"
                label="Укажите формат объекта (кватиры - из шахматки)"
                no-data-text="Пусто"
                required
              ></v-select>
            </v-container>

            <!-- отделка -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-select
                v-model="selectedCurrentTypeTrim"
                item-title="nameRate"
                :items="[
                  'Черновая',
                  'Базовая',
                  'Получистовая',
                  'Чистовая - WOOD',
                  'Чистовая - STONE',
                  'НЕТ',
                ]"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Укажите текущую отделку объекта (квартиры)"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-select
                v-model="selectedNewTypeTrim"
                item-title="nameRate"
                :items="[
                  'Черновая',
                  'Базовая',
                  'Получистовая',
                  'Чистовая - WOOD',
                  'Чистовая - STONE',
                  'НЕТ',
                ]"
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
                v-model="payReserv"
                :items="['0', '20000', '50000']"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Платная бронь"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-text-field
                v-model="UDS"
                :rules="[(v) => !!v || 'Укажите стоимость объекта']"
                variant="solo"
                label="UDS"
                required
              ></v-text-field>

              <v-select
                v-model="tradeIn"
                :items="['0.00', '45000.00']"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Трейд-ин"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-col>
                <v-row class="align-center mb-4" style="gap: 10px;">
                  <v-list-item-subtitle class="font-weight-black text-wrap"
                    >Скидка на чистовую отделку
                    {{
                      selectedNewTypeTrim && `"${selectedNewTypeTrim}"`
                    }}</v-list-item-subtitle
                  >
                  <v-list-item-subtitle class="font-weight-black text-wrap"
                    >{{ calcDiscountCleanTrim }}
                  </v-list-item-subtitle>
                </v-row>
              </v-col>

              <v-select
                v-model="speedDiscount"
                :items="[
                  { title: '0%', value: 0 },
                  { title: '1%', value: 1 },
                  { title: '2%', value: 2 },
                  { title: '3%', value: 3 },
                ]"
                :return-object="true"
                item-title="title"
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
              <div class="text-h4" style="word-break: break-word">{{ calcDownPayment }}</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Сумма кредита</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">{{ calcCreditSum }}</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Базовая ставка, % годовых</v-list-item-subtitle
              >
              <div class="text-h4 mb-5" style="word-break: break-word">{{ this.getSelectedBankProgram[0]?.standartRate || 0}}</div>
              <v-text-field
                v-model="creditPeriod"
                :rules="downPaymentRules"
                variant="solo"
                label="Срок кредита, месяцев"
                required
              ></v-text-field>
            </v-container>

            <!-- срок субсидирования -->
            <v-container
              fluid
              class="fill-height my-5 px-8 pt-5 rounded-lg bg-grey-lighten-3"
            >
              <v-select
                v-model="selectedSubPeriod"
                :items="getArraySubPeriod"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Срок субсидирования, месяцев"
                no-data-text="Пусто"
                required
                @update:modelValue="clearSelectedSubPeriod"
              ></v-select>

              <v-select
                v-model="selectedTotalRates"
                :return-object="true"
                item-title="title"
                :items="getArraySubTerms"
                variant="solo"
                :rules="[(v) => !!v || 'Ставка не выбрана']"
                label="Итоговая ставка с учетом субсидирования, % годовых"
                no-data-text="Пусто"
                required
              ></v-select>
              <v-list-item-subtitle class="font-weight-black text-wrap"
                >Процент удорожания</v-list-item-subtitle
              >
              <div class="text-h4 mb-2" style="word-break: break-word">{{ calcPercApprRate }}%</div>
            </v-container>

            <!--  -->
            <v-container
              fluid
              class="fill-height mt-5 px-6 pt-6 rounded-lg bg-grey-lighten-3"
            >
              <v-list-item-title class="font-weight-black text-wrap mb-5"
                >ИТОГОВАЯ СУММА ДДУ</v-list-item-title
              >
              <v-list-item-subtitle class="font-weight-black text-wrap"
                >Стоимость объекта недвижимости с учетом
                удорожания</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">{{ calcObjectIncludeAppr }}</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Уточнение суммы первоначального взноса</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">{{ calcClarSumFirstContr }}</div>
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Сумма кредита</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">{{ calcSumCredit }}</div>
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
          <div class="text-h4" style="word-break: break-word">{{ calcSumAppr }}</div>
          <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
            >Размер КВ, %</v-list-item-subtitle
          >
          <div class="text-h4" style="word-break: break-word">{{ calcKVSize }}%</div>
          <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
            >Размер КВ, руб.</v-list-item-subtitle
          >
          <div class="text-h4" style="word-break: break-word">{{ calcKVSizeRUB }}</div>
          
        </div>
        <div class="mt-5 px-8 py-7 rounded-lg bg-grey-lighten-3">
          <v-list-item-subtitle class="font-weight-black text-wrap"
            >Новая стоимость объекта с учетом отделки</v-list-item-subtitle
          >
          <div class="text-h4" style="word-break: break-word">
            {{ calcNewCostIncludeTrim }}
          </div>

          <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
            >Итого стоимость объекта с учетом отделки и
            скидки</v-list-item-subtitle
          >
          <div class="text-h4" style="word-break: break-word">
            {{ calcTotalCostObjectIncludeTrimAndDiscount }}
          </div>

          <v-list-item-subtitle class="mt-5 font-weight-black"
            >Стандартный ежемесячный платеж</v-list-item-subtitle
          >

          <div class="text-h4" style="word-break: break-word">
            {{ standartMonthPay }}
          </div>

          <v-col class="d-flex px-0 py-0 justify-space-between">
            <v-col
              cols="6"
              class="px-0 py-0 d-flex flex-column justify-space-between"
            >
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Ежемесячный платеж на период
                субсидирования</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">
                {{ costMonthPeriodSub }}
              </div>
            </v-col>
            <v-col
              cols="5"
              class="px-0 py-0 d-flex flex-column justify-space-between"
            >
              <v-list-item-subtitle class="mt-5 font-weight-black text-wrap"
                >Выгода в месяц</v-list-item-subtitle
              >
              <div class="text-h4" style="word-break: break-word">{{ benefitMonth }}</div>
            </v-col>
          </v-col>

          <v-btn
            color="success"
            block
            class="text-wrap my-4 mt-5"
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
const priceTrim = {
  "Черновая": 0,
  "Базовая": 2000,
  "Получистовая": 2000,
  "Чистовая - WOOD": 18000,
  "Чистовая - STONE": 18000,
  "НЕТ": 0,
};

const discountCl = {
  "Киров": [
    {
      format: 'Студия',
      index: 'Киров-Студия',
      discount: 20,
    },
    {
      format: 'однокомнатная',
      index: 'Киров-Однокомнатная',
      discount: 20,
    },
    {
      format: 'Евро двухкомнатная',
      index: 'Киров-Евро двухкомнатная',
      discount: 20,
    },
    {
      format: 'Двухкомнатная',
      index: 'Киров-Двухкомнатная',
      discount: 20,
    },
    {
      format: 'Евро трехкомнатная',
      index: 'Киров-Евро трехкомнатная',
      discount: 20,
    },
    {
      format: 'Трехкомнатная',
      index: 'Киров-Трехкомнатная',
      discount: 20,
    },
    {
      format: 'Евро четырехкомнатная',
      index: 'Киров-Евро четырехкомнатная',
      discount: 20,
    },
    {
      format: 'Четырехкомнатная',
      index: 'Киров-Четырехкомнатная',
      discount: 20,
    },
  ],
  "Ижевск": [
    {
      format: 'Студия',
      index: 'Ижевск-Студия',
      discount: 20,
    },
    {
      format: 'Однокомнатная',
      index: 'Ижевск-Однокомнатная',
      discount: 20,
    },
    {
      format: 'Евро двухкомнатная',
      index: 'Ижевск-Евро двухкомнаятная',
      discount: 20,
    },
    {
      format: 'Двухкомнатная',
      index: 'Ижевск-Двухкомнаятная',
      discount: 35,
    },
    {
      format: 'Евро Трехкомнатная',
      index: 'Ижевск-Евро трехкомнатная',
      discount: 35,
    },
    {
      format: 'Трехкомнатная',
      index: 'Ижевск-Трехкомнатная',
      discount: 50,
    },
    {
      format: 'Евро Четырехкомнатная',
      index: 'Ижевск-Евро четырекомнатная',
      discount: 50,
    },
    {
      format: 'Четырехкомнатная',
      index: 'Ижевск-Четырекомнатная',
      discount: 50,
    }
  ],
  "Ульяновск": [
    {
      format: 'Студия',
      index: 'Ульяновск-Студия',
      discount: 20,
    },
    {
      format: 'Однокомнатная',
      index: 'Ульяновск-Однокомнатная',
      discount: 20,
    },
    {
      format: 'Евро двухкомнатная',
      index: 'Ульяновск-Евро двухкомнатная',
      discount: 20,
    },
    {
      format: 'Двухкомнатная',
      index: 'Ульяновск-Двухкомнатная',
      discount: 35,
    },
    {
      format: 'Евро трехкомнатная',
      index: 'Ульяновск-Евро трехкомнатная',
      discount: 35,
    },
    {
      format: 'Трехкомнатная',
      index: 'Ульяновск-Трехкомнатная',
      discount: 50,
    },
    {
      format: 'Евро четырехкомнатная',
      index: 'Ульяновск-Евро четырехкомнатная',
      discount: 50,
    },
    {
      format: 'Четырехкомнатная',
      index: 'Ульяновск-Четырехкомнатная',
      discount: 50,
    },
  ],
};

export default {
  name: "HomePage",
  data() {
    return {
      data: null,
      isAlert: false,
      valid: true,
      finishingCbox: false,
      selectedCity: null,
      selectedBank: null,
      selectedBankProgram: null,
      costObject: 0,
      objectArea: 0,
      terraceArea: 0,
      selectedFormatObject: null,
      selectedCurrentTypeTrim: null,
      selectedNewTypeTrim: null,
      payReserv: null,
      UDS: 0,
      tradeIn: null,
      speedDiscount: null,
      downPayment: 0,
      creditPeriod: 0,
      selectedSubPeriod: null,
      selectedTotalRates: null,
      selectedRates: null,
      downPaymentRules: [
        (v) => !!v || "Введите первоначальный взнос",
        (v) => {
          const perc = +this.getSelectedBankProgram[0]?.downPayment.replace('%', '').replace(',', '.');
          if (isNaN(parseFloat(perc)) || !isFinite(perc)) return false;
          return (v && this.downPayment >= perc) ||
            `Первоначальный взнос должен быть не меньше ${perc}%`;
        },
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
      newCostObjectTrim: 0,
      totalCostObjectTrimAndDiscount: 0,
      standartCostMonth: 0,
      monthBenefit: 0,
    };
  },
  async mounted() {
    if (localStorage.data) {
      this.data = JSON.parse(localStorage.data);
    }
    try {
      const data = await fetch("http://localhost:4000/").then((res) =>
        res.json()
      );
      localStorage.data = JSON.stringify(data);
    } catch (err) {console.error(err);}
  },
  computed: {
    // наименование банков
    getBanks() {
      if (!this.data) return [];
      const bankNames = new Set();
      this.data.forEach((obj) => {
        obj.bank && bankNames.add(obj.bank);
      });
      return [...bankNames];
    },
    // общая площадь в расчете отделки
    calcTotalAreaTrim() {
      if (!this.selectedNewTypeTrim) return 0;
      switch (this.selectedNewTypeTrim) {
        case "Базовая" || "Получистовая":
          return this.objectArea;
        case "Чистовая - STONE" || "Чистовая - WOOD":
          return +this.terraceArea > 0
            ? (this.objectArea - this.terraceArea) * 0.3
            : 0;
        default:
          return this.objectArea;
      }
    },
    // стоимость базовой получистовой отделки
    calcCostBasePolCTrim() {
      if (!this.selectedCurrentTypeTrim) return 0;
      const result = +this.objectArea * +priceTrim[this.selectedCurrentTypeTrim];
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    //стоимость на чистовую отделку
    calcCostCleanTrim() {
      if (!this.selectedCurrentTypeTrim || !this.selectedNewTypeTrim) return 0;
      if (
        (this.selectedCurrentTypeTrim === "Базовая" &&
          this.selectedNewTypeTrim === "Чистовая - STONE") ||
        (this.selectedCurrentTypeTrim === "Получистовая" &&
          this.selectedNewTypeTrim === "Чистовая - STONE") ||
        (this.selectedCurrentTypeTrim === "Базовая" &&
          this.selectedNewTypeTrim === "Чистовая - WOOD") ||
        (this.selectedCurrentTypeTrim === "Получистовая" &&
          this.selectedNewTypeTrim === "Чистовая - WOOD")
      )
        return (
          this.calcTotalAreaTrim *
          (+priceTrim[this.selectedNewTypeTrim] -
            +priceTrim[this.selectedCurrentTypeTrim])
        );
      return 0;
    },
    //стоимость черновой
    calcBlackCost() {
      if (!this.selectedCurrentTypeTrim) return 0;
      switch (this.selectedCurrentTypeTrim) {
        case "Черновая":
          return this.costObject;
        case "НЕТ":
          return 0;
        default:
          return (
            (+this.costObject / +this.objectArea -
              +priceTrim[this.selectedCurrentTypeTrim]) *
            +this.objectArea
          );
      }
    },
    // скидка на чистовую отделку
    calcDiscountCleanTrim() {
      if (this.selectedNewTypeTrim !== 'Чистовая - WOOD' && this.selectedNewTypeTrim !== 'Чистовая - STONE') return 0;
      if (!this.selectedCity) return 0;
      const result = discountCl[this.selectedCity].find((obj) => obj.index === `${this.selectedCity}-${this.selectedFormatObject}`);
      if (!result) return 0;
      return (+result.discount / 100) * +this.calcCostCleanTrim;
    },
    // новая стоимость объекта с учетом отделки
    calcNewCostIncludeTrim() {
      const result = +this.calcBlackCost + +this.calcCostBasePolCTrim + +this.calcCostCleanTrim;
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // Итого стоимость объекта с учетом отделки и скидки
    calcTotalCostObjectIncludeTrimAndDiscount() {
      const usd = (+this.calcNewCostIncludeTrim - +this.payReserv) * 0.1;
      if (this.UDS > +usd) return 0;
      const i =
        +this.calcBlackCost +
        +this.calcCostBasePolCTrim +
        +this.calcCostCleanTrim -
        +this.calcDiscountCleanTrim;

      const result =
        i -
        +this.payReserv -
        +this.UDS -
        +this.tradeIn -
        (i - +this.payReserv - +this.UDS - +this.tradeIn) * (this.speedDiscount?.value / 100);
      if (isNaN(parseFloat(result)) || !isFinite(result)) return 0;
      return result.toFixed(2);
    },
    // сумма первоначального взноса
    calcDownPayment() {
      const result = +this.calcTotalCostObjectIncludeTrimAndDiscount * (+this.downPayment / 100);
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // сумма кредита
    calcCreditSum() {
      const result = +this.calcTotalCostObjectIncludeTrimAndDiscount - +this.calcDownPayment;
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // размер кв, %
    calcKVSize() {
      const program = this.getSelectedBankProgram.find((obj) => {
        return obj.discountPeriod === this.selectedSubPeriod && obj.rateGracePeriod === this.selectedTotalRates?.title && obj
      });
      return +program?.sizeCV.replace("%", "").replace(",", ".") || 0;
    },
    // выгода в месяц
    benefitMonth() {
      const result = +this.standartMonthPay - +this.costMonthPeriodSub;
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // ежемесячный платеж на период субсидирования
    costMonthPeriodSub() {
      console.log(+this.calcSumCredit, (+this.selectedTotalRates?.value / 100 / 12), (1 + +this.calcSumCredit / +this.creditPeriod))
      const result =
        +this.calcSumCredit *
        ((+this.selectedTotalRates?.value / 100 / 12) * 1) + +this.calcSumCredit / +this.creditPeriod;
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // размер кв, руб
    calcKVSizeRUB() {
      const result = (+this.calcKVSize / 100) * +this.calcSumCredit;
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // сумма удорожания
    calcSumAppr() {
      const result = +this.calcObjectIncludeAppr - +this.calcTotalCostObjectIncludeTrimAndDiscount;
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // стоимость объекта недвижимости с учетом удорожания
    calcObjectIncludeAppr() {
      const result =
        +this.calcTotalCostObjectIncludeTrimAndDiscount *
        (1 + +this.calcPercApprRate / 100);
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // уточнение суммы первоначального взноса
    calcClarSumFirstContr() {
      const result = +this.calcObjectIncludeAppr * (+this.downPayment / 100);
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // сумма кредита
    calcSumCredit() {
      const result = +this.calcObjectIncludeAppr - +this.calcClarSumFirstContr;
      if (isNaN(parseFloat(result)) || !isFinite(result) || !result) return 0;
      return result.toFixed(2);
    },
    // массив срока субсидирования
    getArraySubPeriod() {
      const program = this.getSelectedBankProgram;
      if (!program.length) return [];
      const discountPeriod = program.map((obj) => {
        return obj.discountPeriod;
      });
      if (!discountPeriod.length) return [];
      return [...new Set(discountPeriod)];
    },
    // массив ставок с учетом субсидий
    getArraySubTerms() {
      const terms = this.getSelectedBankProgram.filter((obj) => obj.discountPeriod === this.selectedSubPeriod);
      if (!terms.length) return [];
      const rateGracePeriod = terms.map((obj) => {
        return {
          title: obj.rateGracePeriod,
          value: obj.rateGracePeriod.replace("%", "").replace(",", "."),
        };
      });
      if (!rateGracePeriod.length) return [];
      return rateGracePeriod;
    },
    // процент удорожания
    calcPercApprRate() {
      if (!this.calcKVSize) return 0;
      let perc = 0;
      if (+this.downPayment <= 20 && (+this.selectedTotalRates?.value > 1.5 && +this.selectedTotalRates?.value <= 2))
        perc = 1;
      else if (+this.downPayment <= 20 && (+this.selectedTotalRates?.value > 1 && +this.selectedTotalRates?.value <= 1.5))
        perc = 2;
      else if (+this.downPayment <= 20 && (+this.selectedTotalRates?.value > 0.5 && +this.selectedTotalRates?.value <= 1))
        perc = 3;
      else if (+this.downPayment <= 20 && (+this.selectedTotalRates?.value > 0 && +this.selectedTotalRates?.value <= 0.5))
        perc = 4;

      const result =
        (+this.calcKVSize / 100 +
          0.5 / 100 +
          perc / 100) *
        100;

      if (isNaN(parseFloat(result)) || !isFinite(result)) return 0;
      return result.toFixed(2);
    },
    // Стандартный ежемесячный платеж
    standartMonthPay() {
      const standartRate = this.getSelectedBankProgram[0]?.standartRate
        .replace("%", "")
        .replace(",", ".");
      const result =
        (+this.calcCreditSum *
          (+standartRate / 100 / 12) *
          +this.creditPeriod) / +this.creditPeriod +
        +this.calcCreditSum / +this.creditPeriod;

      if (isNaN(parseFloat(result)) || !isFinite(result)) return 0;
      return result.toFixed(2);
    },
    // список программ по банку
    getBankPrograms() {
      if (!this.data) return [];
      const selectedBank = this.data.filter((obj) => obj.bank === this.selectedBank);
      const array = selectedBank.map((obj) => obj.bankProgram);
      return [...new Set(array)];
    },
    // получить выбранную программу
    getSelectedBankProgram() {
      if (!this.data) return [];
      const selectedProgram = this.data.filter((obj) => obj.bank === this.selectedBank).filter((obj) => obj.bankProgram === this.selectedBankProgram);
      return selectedProgram;
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
    clearSelectedCreditOrgan() {
      this.selectedBankProgram = null;
      this.selectedRates = null;
      this.selectedSubPeriod = null;
      this.selectedTotalRates = null;
    },
    clearSelectedCreditProgram() {
      this.selectedSubPeriod = null;
      this.selectedTotalRates = null;
      this.selectedRates = null;
    },
    clearSelectedSubPeriod() {
      this.selectedTotalRates = null;
    }
  },
};
</script>
