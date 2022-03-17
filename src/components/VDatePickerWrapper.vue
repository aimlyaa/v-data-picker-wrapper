<template>
  <v-container>
    <v-row>
      <v-col
        cols="12"
        lg="6"
      >
        <v-menu
          ref="showCalendar"
          v-model="showCalendar"
          :close-on-content-click="false"
          transition="scale-transition"
          offset-y
          max-width="290px"
          min-width="auto"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-text-field
              v-model="dateFormatted"
              label="Date"
              persistent-hint
              prepend-icon="mdi-calendar"
              v-bind="attrs"
              @blur="date = parseDate(dateFormatted)"
              v-on="on"
            ></v-text-field>
          </template>
          <v-date-picker
            v-model="date"
            no-title
            @input="showCalendar = false"
          ></v-date-picker>
        </v-menu>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
  import moment from 'moment';
  export default {
    props: ["setDate", "format"],
    data: function () {
    return {
      // дата в формате YYYY-MM-DD. Ќужно дл€ v-data-picker, чтобы он распозновал дату
      date: null,
      // форматированна€ дата, которую видит пользователь
      dateFormatted: null,
      // ƒата, которую передали в пропсах при инициализации компонента
      propsDate: null,
      // ѕоказывать ли сразу календарь
      showCalendar: false,
      }
    },

    mounted() {
      // ѕровер€ем дату, полученную при инициализации
      let isSetDateValid = moment(this.setDate, this.format, true).isValid();
      // ≈сли переданна€ дата не валидна, то присваваем сегодн€шнюю дату в нужном формате (который передали в пропсах)
      let validDate = isSetDateValid ? this.setDate : moment().format(this.format);
      this.setPropsDate(validDate);
      // ‘ормат даты всегда YYYY-MM-DD. 
      this.date = this.propsDate;
      // ‘ормат даты зависит от формата, переданного в props
      this.dateFormatted = moment(this.date).format(this.format);
      // ѕредупреждение, что переданна€ дата была невалидна
      if (!isSetDateValid) { console.log("Props date was invalid") }
    },

    watch: {
      date (val) {
        // провер€ем введеную дата на корректностьь
        let isDateValid = moment(val, this.format, true).isValid() || 
                          moment(val).isValid();
        // если не дата не прошла валидацию, то устанавливаем еЄ на сегнодн€
        if (!isDateValid) {
          val = moment().format('YYYY-MM-DD');
        }
        // форматируем новую дату в переданный формат
        this.dateFormatted = this.formatDate(val);
        // устанавливаем введеную дату
        this.date = moment(val).format('YYYY-MM-DD');
        // ¬озвращаем родительскому элементу новую дату в переданном формате
        this.returnDateToParentComponent(this.date);
      },
    },

    methods: {
      formatDate (date) {
        return moment(date).format(this.format);
      },
      parseDate (date) {
        return moment(date, this.format).format('YYYY-MM-DD');
      },
      setPropsDate (date) {
        this.propsDate = moment(date, this.format).format('YYYY-MM-DD');
      },
      returnDateToParentComponent (date) {
        this.$emit("datechanged", moment(date).format(this.format));
      }
    },
  }
</script>