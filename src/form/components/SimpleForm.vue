<script setup lang="ts">
import { useVuelidate } from '@vuelidate/core'
import { helpers, maxLength, minLength, required } from '@vuelidate/validators'
import type { Ref } from 'vue'

enum Categories {
  Sustainability = 'sustainability',
  Nature = 'nature',
  AnimalWelfare = 'animal welfare',
  Housing = 'housing',
  Education = 'education',
  Food = 'food',
  Community = 'community',
}

const categories = ref([
  Categories.Sustainability,
  Categories.Nature,
  Categories.AnimalWelfare,
  Categories.Housing,
  Categories.Education,
  Categories.Food,
  Categories.Community,
])

interface Extras {
  catering: boolean
  music: boolean
}

interface Event {
  category: string
  title: string
  description: string
  location: string
  pets: number
  extras: Extras
}

const getInitialFormData = () => ({
  category: '',
  title: '',
  description: '',
  location: '',
  pets: 1,
  extras: {
    catering: false,
    music: false,
  },
})

const event: Event = reactive(getInitialFormData())

const titleFieldMinLength = ref(4)
const descriptionFieldMinLength = ref(20)
const descriptionFieldMaxLength = ref(100)

const validationRules = computed(() => ({
  category: { required: helpers.withMessage('Category is required', required) },
  title: {
    required: helpers.withMessage('Title is required', required),
    minLength: minLength(titleFieldMinLength),
  },
  description: {
    required: helpers.withMessage('Description is required', required),
    minLength: minLength(descriptionFieldMinLength),
    maxLength: maxLength(descriptionFieldMaxLength),
  },
  location: { required: helpers.withMessage('Location is required', required) },
}))

const v$ = useVuelidate(validationRules, event, { $lazy: true })

interface PetOption {
  label: string
  value: number
}

const petOptions: Ref<PetOption[]> = ref([
  {
    label: 'Yes',
    value: 1,
  },
  {
    label: 'No',
    value: 0,
  },
])

const resetFormData = () => Object.assign(event, getInitialFormData())
const resetFormValidation = () => v$.value.$reset()
const resetForm = () => {
  resetFormData()
  resetFormValidation()
}

async function onSubmit() {
  const isFormCorrect = await v$.value.$validate()

  if (!isFormCorrect)
    return

  try {
    const response = await fetch('https://my-json-server.typicode.com/denys-petryniak/mastering-vue/events', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(event),
    })

    const data: Event = await response.json()

    // eslint-disable-next-line no-console
    console.log(data)
    resetForm()
  }
  catch (error) {
    console.error(error)
    resetForm()
  }
}
</script>

<template>
  <div>
    <h1 class="title">
      Create an event
    </h1>
    <form @submit.prevent="onSubmit">
      <BaseSelect
        v-model="event.category"
        :options="categories"
        label="Select a category"
        :errors="v$.category.$errors"
        @blur="v$.category.$touch"
      />
      <fieldset>
        <legend>Name & describe your event</legend>
        <BaseInput
          v-model="event.title"
          label="Title"
          type="text"
          :errors="v$.title.$errors"
          @blur="v$.title.$touch"
        />
        <BaseInput
          v-model="event.description"
          label="Description"
          type="text"
          :errors="v$.description.$errors"
          @blur="v$.description.$touch"
        />
      </fieldset>
      <fieldset>
        <legend>Where is your event?</legend>
        <BaseInput
          v-model="event.location"
          label="Location"
          type="text"
          :errors="v$.location.$errors"
          @blur="v$.location.$touch"
        />
      </fieldset>
      <fieldset>
        <legend>Pets</legend>
        <p class="text">
          Are pets allowed?
        </p>
        <div>
          <BaseRadioGroup
            v-model="event.pets"
            :options="petOptions"
            name="pets"
          />
        </div>
      </fieldset>
      <fieldset>
        <legend>Extras</legend>
        <div>
          <BaseCheckbox
            v-model="event.extras.catering"
            label="Catering"
          />
        </div>
        <div>
          <BaseCheckbox
            v-model="event.extras.music"
            label="Live music"
          />
        </div>
      </fieldset>
      <BaseButton
        type="submit"
        :disabled="v$.$errors.length"
        class="-fill-gradient mb-2"
      >
        Submit
      </BaseButton>
      <p
        v-if="v$.$errors.length"
        class="errorMessage"
      >
        Please fill out the required fields
      </p>
    </form>
  </div>
</template>

<style scoped>
form {
  width: 500px;
  margin: 0 auto;
}

.mb-2 {
  margin-bottom: 24px;
}

.title {
  text-align: center;
}

.text {
  margin: 0 0 12px 0;
}
</style>
