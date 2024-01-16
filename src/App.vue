<template>
  <div class="flex h-screen items-center justify-center sm:px-6 md:px-4 md:py-12 lg:px-8">
    <div>
      <div
        class="mx-auto w-auto bg-cover bg-center [height:250px] md:rounded-t-xl"
        style="background-image: url(/images/screaming.webp)"
        role="img"
        aria-label="A stick figure screaming and pulling their hair out"
      ></div>
      <div class="px-10 py-10">
        <form class="w-full max-w-md space-y-8" @submit.prevent="checkForm">
          <div>
            <h2 class="text-center text-3xl font-extrabold text-gray-900 dark:text-white">
              Salary Calculator
            </h2>
            <p class="text-center text-sm dark:text-white">
              v{{ version }} | Last updated: 16/01/2024 | Click
              <a
                href="https://ridwanazeez.notion.site/Salary-Calculator-Update-Notes-ad551e6f8c18465e8fed5517616b0184"
                class="underline"
                target="_blank"
              >
                here</a
              >
              to see what's new
            </p>
            <p class="mt-4 text-center font-medium text-gray-500 dark:text-white">
              Disclaimer: This website is in no way affiliated with the Guyana Revenue Authority
              (GRA). The code is based on the
              <a
                href="https://www.gra.gov.gy/quick-links/calculators/income-tax-calculator/"
                class="underline"
                target="_blank"
                >official calculator</a
              >
              from the GRA and there may be some inaccuracies. Trust the figures here at your own
              risk.
              <br />
            </p>
            <div v-if="errors.length">
              <p class="mt-4 text-center text-2xl font-medium text-red-500">
                Error! Please fill in all the fields!
              </p>
              <p v-for="error in errors" :key="error" class="font-bold dark:text-white">
                {{ error }}
              </p>
            </div>
          </div>
          <div class="space-y-6">
            <div class="grid grid-cols-6 gap-6">
              <div class="col-span-6 sm:col-span-3">
                <label
                  for="basicSalary"
                  class="block text-sm font-medium text-gray-700 dark:text-white"
                >
                  Basic Salary
                </label>
                <div class="relative">
                  <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
                    <span class="text-gray-500 sm:text-sm">$</span>
                  </div>
                  <input
                    id="basicSalary"
                    v-model="form.basicSalary"
                    type="number"
                    name="basicSalary"
                    placeholder="100000"
                    class="mt-1 block w-full rounded-md border-gray-300 pl-7 shadow-sm focus:border-green-500 focus:ring-green-500 sm:text-sm"
                  />
                </div>
              </div>
              <div class="col-span-6 sm:col-span-3">
                <label
                  for="allowances"
                  class="block text-sm font-medium text-gray-700 dark:text-white"
                >
                  Total Allowances
                </label>
                <div class="relative">
                  <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
                    <span class="text-gray-500 sm:text-sm">$</span>
                  </div>
                  <input
                    id="allowances"
                    v-model="form.allowances"
                    type="number"
                    name="allowances"
                    placeholder="100000"
                    class="mt-1 block w-full rounded-md border-gray-300 pl-7 shadow-sm focus:border-green-500 focus:ring-green-500 sm:text-sm"
                  />
                </div>
              </div>
            </div>
            <div class="grid grid-cols-6 gap-6">
              <div class="col-span-6 sm:col-span-3">
                <label class="relative flex gap-x-3" @click="toggleThreshold()">
                  <div class="flex h-6 items-center">
                    <input
                      id="newThreshold"
                      name="newThreshold"
                      type="checkbox"
                      v-model="newThreshold"
                      class="h-4 w-4 rounded border-gray-300 text-green-600 focus:ring-green-600"
                    />
                  </div>
                  <div class="text-sm leading-6">
                    <label for="newThreshold" class="font-medium text-gray-900 dark:text-white"
                      >New Threshold</label
                    >
                  </div>
                </label>
              </div>
            </div>
            <div>
              <button
                type="submit"
                class="group relative flex w-full justify-center rounded-md border border-transparent bg-green-100 px-4 py-2 font-medium text-green-700 hover:bg-green-700 hover:text-white focus:outline-none focus:ring-2 focus:ring-offset-2 dark:bg-green-500 dark:text-white dark:hover:bg-green-700"
                @click="checkForm, calculateTax()"
              >
                Calculate Salary
              </button>
            </div>
          </div>
        </form>
        <div class="mt-6 space-y-6">
          <button
            @click="toggleDark()"
            class="w-full rounded-md text-xs hover:text-gray-300 dark:text-white"
          >
            Toggle Dark Theme
          </button>
          <p class="text-center font-medium text-gray-300">
            Made with ♥ by
            <a href="https://ridwanazeez.github.io/" class="underline" target="_blank">me</a>
            <br />
          </p>
        </div>
      </div>
    </div>
  </div>
  <!-- Results Modal -->
  <TransitionRoot as="template" :show="(show = show)">
    <Dialog as="div" class="relative z-10" :open="show" @close="show = !show">
      <TransitionChild
        as="template"
        enter="ease-out duration-300"
        enter-from="opacity-0"
        enter-to="opacity-100"
        leave="ease-in duration-200"
        leave-from="opacity-100"
        leave-to="opacity-0"
      >
        <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" />
      </TransitionChild>

      <div class="fixed inset-0 z-10 overflow-y-auto">
        <div class="flex min-h-full items-center justify-center p-4 text-center sm:p-0">
          <TransitionChild
            as="template"
            enter="ease-out duration-300"
            enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
            enter-to="opacity-100 translate-y-0 sm:scale-100"
            leave="ease-in duration-200"
            leave-from="opacity-100 translate-y-0 sm:scale-100"
            leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
          >
            <DialogPanel
              class="relative transform overflow-hidden rounded-lg text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg"
            >
              <div class="bg-white px-4 pb-4 pt-5 dark:bg-gray-900 dark:text-white sm:p-6 sm:pb-4">
                <div>
                  <div class="mt-3 sm:mt-0">
                    <DialogTitle
                      as="h3"
                      class="pb-3 text-center text-xl font-bold text-gray-900 dark:text-white"
                    >
                      Results
                    </DialogTitle>
                    <table class="w-full table-auto">
                      <tr>
                        <th class="text-left" colspan="2">Basic Salary:</th>
                        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="text-right">
                          {{ '$ ' + Math.round(this.form.basicSalary).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                      <tr>
                        <th class="text-left" colspan="2">Total Allowances:</th>
                        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="text-right">
                          {{ '$ ' + Math.round(this.form.allowances).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                      <tr>
                        <th class="text-left" colspan="2">Gross Salary</th>
                        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="text-right">
                          {{ '$ ' + Math.round(this.grossSalary).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                      <tr>
                        <th class="text-left" colspan="2">Tax Threshold</th>
                        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="text-right">
                          {{ '$ ' + Math.round(this.taxThreshold).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                      <tr>
                        <th class="text-left" colspan="2">Untaxable Income</th>
                        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="text-right">
                          {{ '$ ' + Math.round(this.untaxableIncome).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                      <tr>
                        <th class="text-left" colspan="2">Taxable Income</th>
                        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="text-right">
                          {{ '$ ' + Math.round(this.taxableIncome).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                      <tr class="">
                        <th class="pt-2 text-left font-bold" colspan="2">Total Monthly Income</th>
                        <td class="pt-2">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="pt-2 text-right font-bold">
                          {{ '$ ' + Math.round(this.totalIncome).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                    </table>
                  </div>
                </div>
              </div>
              <div
                class="bg-gray-50 px-4 py-3 dark:bg-gray-800 sm:flex sm:flex-row-reverse sm:px-6"
              >
                <button
                  type="button"
                  class="inline-flex w-full justify-center rounded-md border border-transparent bg-red-600 px-4 py-2 text-base font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 sm:ml-3 sm:w-auto sm:text-sm"
                  @click="(show = !show), reset()"
                >
                  Close
                </button>
              </div>
            </DialogPanel>
          </TransitionChild>
        </div>
      </div>
    </Dialog>
  </TransitionRoot>
</template>

<script>
import { Dialog, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from '@headlessui/vue'
import { useDark, useToggle } from '@vueuse/core'
import { version } from '../package.json'

const isDark = useDark()
const toggleDark = useToggle(isDark)
const salaryForm = {
  basicSalary: '',
  allowances: ''
}

export default {
  components: {
    // eslint-disable-next-line vue/no-reserved-component-names
    Dialog,
    DialogPanel,
    DialogTitle,
    TransitionChild,
    TransitionRoot
  },
  data() {
    return {
      form: salaryForm,
      grossSalary: 0,
      untaxableIncome: 0,
      taxableIncome: 0,
      nisDeduction: 0,
      incomeTax: 0,
      netIncome: 0,
      totalIncome: 0,
      taxThreshold: 85000,
      taxThreshold1: 0,
      taxThreshold2: 0,
      show: false,
      version: version,
      isDark,
      newThreshold: false,
      toggleDark,
      errors: []
    }
  },
  computed: {},
  methods: {
    // Checks if form is valid
    checkForm(e) {
      this.errors = []
      if (this.form.basicSalary && this.form.allowances) {
        this.show = !this.show
        return true
      } else {
        if (!this.form.basicSalary) {
          this.errors.push('Please enter your salary')
        }
        if (!this.form.allowances) {
          this.errors.push('Please enter your total allowances')
        }
      }
      e.preventDefault()
    },
    // Main tax calculation function
    calculateTax() {
      // Calculate untaxable income
      if (this.form.basicSalary > this.taxThreshold && this.form.basicSalary <= 195000) {
        this.untaxableIncome = this.taxThreshold
      } else if (this.form.basicSalary < this.taxThreshold) {
        this.untaxableIncome = this.form.basicSalary
      } else {
        this.untaxableIncome = this.form.basicSalary * 0.3333333333333333
      }
      // Calculate NIS
      if (this.form.basicSalary < 256800) {
        this.nisDeduction = this.form.basicSalary * 0.056
      } else {
        this.nisDeduction = 256800 * 0.056
      }
      // Calculate taxable income
      if (this.form.basicSalary > this.taxThreshold) {
        this.taxableIncome = this.form.basicSalary - (this.untaxableIncome + this.nisDeduction)
      } else {
        this.taxableIncome = 0
      }
      // Calculate income tax
      if (this.taxableIncome < 150000) {
        this.taxThreshold1 = this.taxableIncome * 0.28
      } else {
        this.taxThreshold1 = 150000 * 0.28
      }
      if (this.taxableIncome - 150000 > 0) {
        this.taxThreshold2 = (this.taxableIncome - 150000) * 0.4
      } else {
        this.taxThreshold2 = 0
      }
      this.incomeTax = this.taxThreshold1 + this.taxThreshold2
      // Calculate gross salary
      this.grossSalary = this.form.basicSalary + this.form.allowances
      // Calculate net income
      this.netIncome = this.form.basicSalary - (this.nisDeduction + this.incomeTax)
      // Calculate total income
      this.totalIncome = this.netIncome + this.form.allowances
    },
    toggleThreshold() {
      if (this.newThreshold) {
        this.taxThreshold = 85000
      } else {
        this.taxThreshold = 100000
      }
    },
    // Resets all values
    reset() {
      this.grossSalary = ''
      this.untaxableIncome = ''
      this.taxableIncome = ''
      this.nisDeduction = ''
      this.incomeTax = ''
      this.netIncome = ''
      this.totalIncome = ''
      this.taxThreshold1 = ''
      this.taxThreshold2 = ''
      this.form.basicSalary = ''
      this.form.allowances = ''
    },
    logFormData() {
      let formValues = []
      formValues.push({
        'Basic Salary: ': this.basicSalary,
        'Allowances: ': this.allowances,
        'Gross Salary: ': this.grossSalary,
        'Untaxable Income: ': this.untaxableIncome,
        'Taxable Income: ': this.taxableIncome,
        'NIS Deduction: ': this.nisDeduction,
        'Income Tax: ': this.incomeTax,
        'Net Income: ': this.netIncome,
        'Total Income: ': this.totalIncome
      })
      console.log('Form Data:', formValues)
    }
  }
}
</script>
