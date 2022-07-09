<template>
  <v-container
    style="max-width: 800px"
    class="fill-height justify-center d-flex"
  >
    <v-row class="fill-height">
      <v-col>
        <v-form ref="form" v-model="valid">
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

          <v-checkbox
            v-model="checkbox"
            :rules="[(v) => !!v || 'You must agree to continue!']"
            label="Отделка"
            required
          ></v-checkbox>

          <v-text-field
            v-model="checkbox"
            :rules="nameRules"
            variant="solo"
            label="Площадь"
            required
          ></v-text-field>

          <v-text-field
            v-model="name"
            :rules="nameRules"
            variant="solo"
            label="Стоимость объекта"
            required
          ></v-text-field>

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
            label="Первоначальный взнос %"
            required
          ></v-text-field>

          <v-text-field
            v-model="name"
            :rules="nameRules"
            variant="solo"
            label="Срок ипотеки мес."
            required
          ></v-text-field>

          <v-list-header class="text-red"
            >Увеличить стоимость на 22%</v-list-header
          >

          <v-list-subheader
            >Стоимость объекта недвижимости с предоставлением
            субсидии</v-list-subheader
          >

          <v-text-field
            v-model="name"
            :rules="nameRules"
            variant="solo"
            required
          ></v-text-field>

          <v-list-subheader>Ежемесячный платеж</v-list-subheader>

          <v-text-field
            v-model="name"
            :rules="nameRules"
            variant="solo"
            required
          ></v-text-field>

          <v-btn
            :disabled="!valid"
            color="secondary"
            variant="outlined"
            class="my-4 w-100"
            @click="validate"
            size="x-large"
          >
            Проверить результат
          </v-btn>

          <v-btn
            :disabled="!valid"
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
  </v-container>
</template>

<script>
export default {
  name: "HomePage",
  data() {
    return {};
  },
};
</script>
