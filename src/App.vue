<template>
  <div class="flex items-center justify-center px-4 py-12 sm:px-6 lg:px-8">
    <div class="">
      <div
        class="mx-auto w-auto rounded-t-xl bg-cover bg-center [height:250px]"
        style="background-image: url(/screaming.webp)"
        role="img"
        aria-label="Image of an empty wallet"
      ></div>
      <div class="px-10 py-10">
        <form class="w-full max-w-md space-y-8" @submit.prevent="checkForm">
          <div>
            <h2 class="text-center text-3xl font-extrabold text-gray-900 dark:text-white">
              Salary Calculator
            </h2>
            <p class="text-center text-sm dark:text-white">
              v{{ version }} | Last updated: 01/08/2023 | Click
              <a
                href="https://ridwanazeez.notion.site/Salary-Calculator-Update-Notes-ad551e6f8c18465e8fed5517616b0184?pvs=4"
                class="underline"
              >
                here
              </a>
              to see what's new
            </p>
            <p class="mt-4 text-center font-medium text-gray-500 dark:text-white">
              Disclaimer: This website is in no way affiliated with the Guyana Revenue Authority
              (GRA). The code is based on the
              <a
                href="https://www.gra.gov.gy/quick-links/calculators/income-tax-calculator/"
                class="underline"
              >
                official calculator
              </a>
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
                  v-model="basicSalary"
                  type="number"
                  name="basicSalary"
                  placeholder="100000"
                  class="mt-1 block w-full rounded-md border-gray-300 pl-7 shadow-sm focus:border-green-500 focus:ring-green-500 sm:text-sm"
                />
              </div>
            </div>
            <div
              class="grid grid-cols-6 items-end gap-6"
              v-for="(allowance, i) in allowances"
              :key="i"
            >
              <div class="col-span-2 sm:col-span-2">
                <label
                  for="allowanceAmount"
                  class="block text-sm font-medium text-gray-700 dark:text-white"
                >
                  Allowance Amount
                </label>
                <div class="relative">
                  <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
                    <span class="text-gray-500 sm:text-sm">$</span>
                  </div>
                  <input
                    id="allowanceAmount"
                    v-model="allowance.allowanceAmount"
                    type="number"
                    name="allowanceAmount"
                    placeholder="100000"
                    class="mt-1 block w-full rounded-md border-gray-300 pl-7 shadow-sm focus:border-green-500 focus:ring-green-500 sm:text-sm"
                  />
                </div>
              </div>
              <div class="col-span-2 sm:col-span-2">
                <label
                  for="allowanceType"
                  class="block text-sm font-medium text-gray-700 dark:text-white"
                >
                  Allowance Type
                </label>
                <select
                  id="allowanceType"
                  v-model="allowance.allowanceType"
                  name="allowanceType"
                  class="mt-1 block w-full rounded-md border border-gray-300 bg-white px-3 py-2 shadow-sm focus:border-blue-500 focus:outline-none focus:ring-blue-500 sm:text-sm"
                >
                  <option value="" disabled selected>Choose Allowance Type</option>
                  <option value="Housing">Housing</option>
                  <option value="Communication">Communication</option>
                  <option value="Transportation">Transportation</option>
                </select>
              </div>
              <div class="col-span-1 sm:col-span-1">
                <button
                  type="button"
                  class="flex w-full justify-center rounded-md border border-transparent bg-green-100 px-2 py-2 font-medium text-green-700 hover:bg-green-700 hover:text-white focus:outline-none focus:ring-2 focus:ring-offset-2 dark:bg-green-500 dark:text-white dark:hover:bg-green-700"
                  @click="addAllowance()"
                >
                  <PlusSmallIcon class="h-6 w-6 text-white" />
                </button>
              </div>
              <div class="col-span-1 sm:col-span-1">
                <button
                  type="button"
                  class="flex w-full justify-center rounded-md border border-transparent bg-red-100 px-2 py-2 font-medium text-red-700 hover:bg-red-700 hover:text-white focus:outline-none focus:ring-2 focus:ring-offset-2 dark:bg-red-500 dark:text-white dark:hover:bg-red-700"
                  @click="deleteAllowance(i)"
                >
                  <MinusSmallIcon class="h-6 w-6 text-white" />
                </button>
              </div>
            </div>
            <div>
              <button
                type="button"
                class="group relative flex w-full justify-center rounded-md border border-transparent bg-green-100 px-4 py-2 font-medium text-green-700 hover:bg-green-700 hover:text-white focus:outline-none focus:ring-2 focus:ring-offset-2 dark:bg-green-500 dark:text-white dark:hover:bg-green-700"
                @click="logFormData()"
              >
                Calculate Duty
              </button>
            </div>
          </div>
        </form>
        <button
          @click="toggleDark = !toggleDark"
          class="mt-4 w-full rounded-md text-xs text-white hover:text-gray-300"
        >
          Toggle Dark Theme
        </button>
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
              class="relative transform overflow-hidden rounded-lg bg-white text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg"
            >
              <div class="bg-white px-4 pb-4 pt-5 sm:p-6 sm:pb-4">
                <div class="sm:flex sm:items-start">
                  <div class="mt-3 text-center sm:ml-4 sm:mt-0 sm:text-left">
                    <DialogTitle as="h3" class="pb-3 text-xl font-bold text-gray-900">
                      Results
                    </DialogTitle>
                    <table class="w-full table-auto">
                      <tr>
                        <th class="text-left">Car Cost:</th>
                        <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                        <td class="text-right">
                          {{ '$ ' + Math.round(cost).toLocaleString() + ' ' }}GYD
                        </td>
                      </tr>
                    </table>
                  </div>
                </div>
              </div>
              <div class="bg-gray-50 px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6">
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
import { MinusSmallIcon, PlusSmallIcon } from '@heroicons/vue/24/outline'
import { useDark, useToggle } from '@vueuse/core'
import { version } from '../package.json'

const isDark = useDark()
const toggleDark = useToggle(isDark)

export default {
  components: {
    // eslint-disable-next-line vue/no-reserved-component-names
    Dialog,
    DialogPanel,
    DialogTitle,
    TransitionChild,
    TransitionRoot,
    PlusSmallIcon,
    MinusSmallIcon
  },
  data() {
    return {
      basicSalary: '',
      allowances: [
        {
          allowanceAmount: '',
          allowanceType: ''
        }
      ],
      totalAllowances: '',
      totalTaxableAllowances: '',
      grossSalary: '',
      untaxableIncome: '',
      taxableIncome: '',
      nisDeduction: '0.056',
      incomeTax: '',
      netIncome: '',
      totalIncome: '',
      show: false,
      version: version,
      isDark,
      toggleDark,
      errors: []
    }
  },
  methods: {
    checkForm: function (e) {
      this.logFormData()
      this.errors = []
      if (this.fuel != 'Electric' && this.fuel) {
        if (this.cif && this.exchange_rate && this.age && this.cc && this.fuel) {
          this.show = !this.show
          return true
        }
        if (!this.cif) {
          this.errors.push('CIF required.')
        }
        if (!this.exchange_rate) {
          this.errors.push('Exchange Rate required.')
        }
        if (!this.age) {
          this.errors.push('Vehicle Age required.')
        }
        if (!this.cc) {
          this.errors.push('Vehicle CC required.')
        }
        if (!this.fuel) {
          this.errors.push('Vehicle propulsion type required.')
        }
      } else {
        if (this.cif && this.exchange_rate && this.age && this.fuel) {
          this.show = !this.show
          return true
        }
        if (!this.cif) {
          this.errors.push('CIF required.')
        }
        if (!this.exchange_rate) {
          this.errors.push('Exchange Rate required.')
        }
        if (!this.age) {
          this.errors.push('Vehicle Age required.')
        }
        if (!this.fuel) {
          this.errors.push('Vehicle propulsion type required.')
        }
      }
      e.preventDefault()
    },
    // Main tax calculation function
    calculateTax() {},
    // Resets all form values
    reset() {
      this.basic_salary = ''
      this.allowances = ''
      this.gross_salary = ''
      this.untaxable_income = ''
      this.taxable_income = ''
      this.income_tax = ''
      this.net_income = ''
      this.total_income = ''
    },
    logFormData() {
      let formValues = []
      formValues.push({
        'Basic Salary: ': this.basicSalary,
        'Allowances: ': this.allowances,
        'Total Allowances: ': this.totalAllowances,
        'Total Taxable Allowances: ': this.totalTaxableAllowances,
        'Gross Salary: ': this.grossSalary,
        'Untaxable Income: ': this.untaxableIncome,
        'Taxable Income: ': this.taxableIncome,
        'NIS Deduction: ': this.nisDeduction,
        'Income Tax: ': this.incomeTax,
        'Net Income: ': this.netIncome,
        'Total Income: ': this.totalIncome
      })
      console.log('Form Data:', formValues)
    },
    addAllowance() {
      this.allowances.push({
        allowance_amount: '',
        allowance_type: ''
      })
    },
    deleteAllowance(index) {
      this.allowances.splice(index, 1)
    }
  }
}
</script>
