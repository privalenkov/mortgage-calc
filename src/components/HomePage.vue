<template>
  <v-container class="d-sm-flex">
    <v-col sm="6">
      <v-row class="fill-height">
        <v-col>
          <v-form ref="form" v-model="valid">
            <v-container
              fluid
              class="fill-height my-5 px-10 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-select
                v-model="select"
                :items="items"
                :rules="[(v) => !!v || 'Item is required']"
                label="Банк"
                variant="solo"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-select
                v-model="select"
                :items="items"
                variant="solo"
                :rules="[(v) => !!v || 'Item is required']"
                label="Ипотечная программа"
                no-data-text="Пусто"
                required
              ></v-select>

              <v-select
                v-model="select"
                :items="items"
                variant="solo"
                :rules="[(v) => !!v || 'Item is required']"
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
                v-model="name"
                :rules="nameRules"
                variant="solo"
                label="Стоимость объекта"
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

            <v-container
              fluid
              class="fill-height my-5 px-10 pt-10 rounded-lg bg-grey-lighten-3"
            >
              <v-list-subheader class="font-weight-black"
                >Первоначальный взнос 20.2%</v-list-subheader
              >
              <v-text-field
                v-model="name"
                :rules="nameRules"
                variant="solo"
                label="Первоначальный взнос"
                required
              ></v-text-field>

              <v-text-field
                v-model="name"
                :rules="nameRules"
                variant="solo"
                label="Срок ипотеки мес."
                required
              ></v-text-field>
            </v-container>

            <v-btn
              :disabled="!valid"
              color="secondary"
              variant="outlined"
              class="my-4 w-100 mt-10"
              @click="validate"
              size="x-large"
            >
              Проверить результат
            </v-btn>

            <v-btn
              variant="outlined"
              class="my-4 w-100"
              @click="validate"
              size="x-large"
            >
              Инструкция по работе
            </v-btn>
          </v-form>
        </v-col>
      </v-row>
    </v-col>
    <v-col sm="6">
      <div style="top: 0" class="position-sticky rounded-lg bg-grey-lighten-3">
        <div class="my-5 px-10 py-10">
          <v-list-header class="text-red"
            >Увеличить стоимость на 22%</v-list-header
          >

          <v-list-subheader class="font-weight-black text-wrap"
            >Стоимость объекта недвижимости с предоставлением
            субсидии</v-list-subheader
          >
          <v-list-title class="text-h2"> 0 </v-list-title>

          <v-list-subheader class="font-weight-black"
            >Ежемесячный платеж</v-list-subheader
          >

          <v-list-title class="text-h2"> 0 </v-list-title>
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
      finishingCbox: false,
    };
  },
};
</script>
