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
      // ���� � ������� YYYY-MM-DD. ����� ��� v-data-picker, ����� �� ����������� ����
      date: null,
      // ��������������� ����, ������� ����� ������������
      dateFormatted: null,
      // ����, ������� �������� � ������� ��� ������������� ����������
      propsDate: null,
      // ���������� �� ����� ���������
      showCalendar: false,
      }
    },

    mounted() {
      // ��������� ����, ���������� ��� �������������
      let isSetDateValid = moment(this.setDate, this.format, true).isValid();
      // ���� ���������� ���� �� �������, �� ���������� ����������� ���� � ������ ������� (������� �������� � �������)
      let validDate = isSetDateValid ? this.setDate : moment().format(this.format);
      this.setPropsDate(validDate);
      // ������ ���� ������ YYYY-MM-DD. 
      this.date = this.propsDate;
      // ������ ���� ������� �� �������, ����������� � props
      this.dateFormatted = moment(this.date).format(this.format);
      // ��������������, ��� ���������� ���� ���� ���������
      if (!isSetDateValid) { console.log("Props date was invalid") }
    },

    watch: {
      date (val) {
        // ��������� �������� ���� �� �������������
        let isDateValid = moment(val, this.format, true).isValid() || 
                          moment(val).isValid();
        // ���� �� ���� �� ������ ���������, �� ������������� � �� ��������
        if (!isDateValid) {
          val = moment().format('YYYY-MM-DD');
        }
        // ����������� ����� ���� � ���������� ������
        this.dateFormatted = this.formatDate(val);
        // ������������� �������� ����
        this.date = moment(val).format('YYYY-MM-DD');
        // ���������� ������������� �������� ����� ���� � ���������� �������
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