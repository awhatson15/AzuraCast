<template>
    <form-group
        v-bind="$attrs"
        :id="id"
    >
        <template
            v-if="label || slots.label"
            #label="slotProps"
        >
            <form-label
                :is-required="isRequired"
                v-bind="pickProps(props, formLabelProps)"
            >
                <slot
                    name="label"
                    v-bind="slotProps"
                >
                    {{ label }}
                </slot>
            </form-label>
        </template>

        <template #default>
            <slot
                name="default"
                v-bind="{ id, field, model }"
            >
                <form-multi-check
                    :id="id"
                    v-model="model"
                    :name="name || id"
                    :options="options"
                    :radio="radio"
                    :stacked="stacked"
                >
                    <template
                        v-for="(_, slot) of useSlotsExcept(['default', 'label', 'description'])"
                        #[slot]="scope"
                    >
                        <slot
                            :name="slot"
                            v-bind="scope"
                        />
                    </template>
                </form-multi-check>
            </slot>

            <vuelidate-error
                v-if="isVuelidateField"
                :field="field"
            />
        </template>

        <template
            v-if="description || slots.description"
            #description="slotProps"
        >
            <slot
                v-bind="slotProps"
                name="description"
            >
                {{ description }}
            </slot>
        </template>
    </form-group>
</template>

<script setup lang="ts">
import VuelidateError from "./VuelidateError.vue";
import FormLabel from "~/components/Form/FormLabel.vue";
import FormGroup from "~/components/Form/FormGroup.vue";
import FormMultiCheck from "~/components/Form/FormMultiCheck.vue";
import useSlotsExcept from "~/functions/useSlotsExcept";
import {formFieldProps, useFormField} from "~/components/Form/useFormField";
import {useSlots} from "vue";
import formLabelProps from "~/components/Form/formLabelProps.ts";
import {pickProps} from "~/functions/pickProps.ts";

const props = defineProps({
    ...formFieldProps,
    ...formLabelProps,
    id: {
        type: String,
        required: true
    },
    name: {
        type: String,
        default: null,
    },
    label: {
        type: String,
        default: null
    },
    description: {
        type: String,
        default: null
    },
    options: {
        type: Array,
        required: true
    },
    radio: {
        type: Boolean,
        default: false
    },
    stacked: {
        type: Boolean,
        default: false
    }
});

const slots = useSlots();

const emit = defineEmits(['update:modelValue']);

const {model, isVuelidateField, isRequired} = useFormField(props, emit);
</script>
