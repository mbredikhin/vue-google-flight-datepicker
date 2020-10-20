<template>
  <div
    class="date"
    :class="{ 'is-focus': isFocus, 'is-single': isSingle }"
    role="button"
    :tabIndex="nonFocusable ? '-1' : tabIndex"
    @click.stop="onDateInputFocus"
    :id="
      name === 'START_DATE'
        ? 'start-date-input-button'
        : 'start-date-input-button'
    "
  >
    <CalendarIcon v-if="showIcon" class="icon-calendar" viewBox="0 0 24 24" />

    <div class="selected-date">
      <span v-if="formattedDate">{{ formattedDate }}</span>
      <div class="date-placeholder" v-else>{{ placeholder }}</div>
    </div>
    <div v-if="formattedDate" class="change-date-group">
      <button
        type="button"
        class="btn-outline change-date-button"
        @click.stop="prevDate"
        :tabindex="nonFocusable ? '-1' : '0'"
        disabled="disablePrev"
      >
        <PrevIcon viewBox="0 0 24 24" class="icon-arrow" />
      </button>
      <button
        type="button"
        class="btn-outline change-date-button"
        @click.stop="nextDate"
        :tabindex="nonFocusable ? '-1' : '0'"
        :disabled="disableNext"
      >
        <NextIcon viewBox="0 0 24 24" class="icon-arrow" />
      </button>
    </div>
  </div>
</template>

<script>
import dayjs from "dayjs";
import CalendarIcon from "@/assets/svg/calendar.svg";
import PrevIcon from "@/assets/svg/prev.svg";
import NextIcon from "@/assets/svg/next.svg";

export default {
  components: {
    CalendarIcon,
    PrevIcon,
    NextIcon
  },

  props: {
    showIcon: {
      type: Boolean,
      default: false
    },
    tabIndex: {
      type: String,
      default: ""
    },
    isFocus: {
      type: Boolean,
      default: false
    },
    value: {
      type: Date,
      default: null
    },
    placeholder: {
      type: String,
      default: null
    },
    dateFormat: {
      type: String,
      default: ""
    },
    isSingle: {
      type: Boolean,
      default: false
    },
    name: {
      type: String,
      default: ""
    },
    nonFocusable: {
      type: Boolean,
      default: false
    },
    fromDate: {
      type: Date,
      default: null
    },
    minDate: {
      type: Date,
      default: null
    },
    maxDate: {
      type: Date,
      default: null
    }
  },

  computed: {},

  data() {
    return {
      formattedDate: null,
      disablePrev: false,
      disableNext: false
    };
  },

  methods: {
    prevDate() {
      this.$emit("handleChangeDate", "prev", this.value);
    },
    nextDate() {
      this.$emit("handleChangeDate", "next", this.value);
    },
    onDateInputFocus() {
      if (this.onFocus) this.$emit("onFocus", this.name);
    },
    onValueUpdate() {
      if (this.value) {
        let text = this.value.format("ddd, DD MMM");
        if (this.dateFormat) {
          text = this.value.format(this.dateFormat);
        }
        this.formattedDate = text;

        if (
          (this.minDate &&
            dayjs(this.minDate)
              .add(1, "day")
              .isAfter(this.value, "date")) ||
          (name === "END_DATE" &&
            this.value.isBefore(this.fromDate.add(1, "day"), "date"))
        ) {
          this.disablePrev = true;
        } else {
          this.disablePrev = false;
        }

        if (
          this.maxDate &&
          dayjs(this.maxDate)
            .subtract(1, "day")
            .isBefore(this.value, "date")
        ) {
          this.disableNext = true;
        } else {
          this.disableNext = false;
        }
      } else {
        this.formattedDate = null;
      }
    }
  },

  watch: {
    value() {
      this.onValueUpdate();
    },
    fromDate() {
      this.onValueUpdate();
    }
  }
};
</script>

<style></style>
