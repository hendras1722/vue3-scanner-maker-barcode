<template>
  <svg :id="props.options.format" ref="barcode"></svg>
</template>

<script lang="ts" setup>
import JsBarcode from "jsbarcode";
import { PropType, onMounted } from "vue";

interface Options {
  format: "code128"
    | "code39"
    | "ean13"
    | "ean8"
    | "ean5"
    | "ean2"
    | "upc"
    | "itf-14"
    | "msi"
    | "pharmacode";
  width?: number;
  height?: number;
  displayValue?: boolean;
  fontOptions?: {
    name: string;
    size: number;
  };
  font?: string;
  textAlign?: string;
  textPosition?: string;
  textMargin?: string;
  fontSize?: number;
  background?: `#${string}`;
  lineColor?: `#${string}`;
  margin?: number;
  marginTop?: number | undefined;
  marginBottom?: number | undefined;
  marginLeft?: number | undefined;
  marginRight?: number | undefined;
  padding?: number;
  valid?: (valid: boolean) => void;
}

const props = defineProps({
  options: {
    type: Object as PropType<Options>,
    default: () => ({}),
  },
  value: {
    type: String,
    default: "",
    required: true
  },
});


onMounted(() => {
  JsBarcode('#'+props.options.format, props.value);
});
</script>
