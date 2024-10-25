<template>
  <transition name="fade-modal">
    <div class="modal">
      <div
        class="modal__hider"
        @click="
          setReviewModalForm({
            visibility: false,
            id: null,
            type: undefined,
          })
        "
      ></div>

      <div class="modal__inner">
        <div class="modal__label">
          <span class="modal__label-logo">
            <img
              :src="`https://www.google.com/s2/favicons?domain=${reviewModalForm.icon}`"
              alt=""
            />
          </span>
          {{ reviewModalForm.brokerName }}
        </div>

        <button
          class="modal__close"
          @click="
            setReviewModalForm({
              visibility: false,
              id: null,
              type: undefined,
            })
          "
        >
          <icon-cross/>
        </button>

        <div class="modal__content">
          <form class="modal__form">
            <div class="modal__title">
              {{ $t("brokerReviews.modalReview.title") }}
            </div>
            <div class="modal__desc">
              {{
                $t(
                  "brokerReviews.modalReview.desc",
                  {
                    name: reviewModalForm.brokerName,
                    type: $t('brokerReviews.modalReview.type.' + reviewModalForm.type.toLowerCase()),
                  }
                )
              }}
            </div>
            <div class="modal__form-content">
              <div class="steps-wrap">
                <fieldset
                  :class="['thanks-block', { '--current': currentStep === 4 }]"
                >
                  <h3 class="thanks-block--title">
                    {{ $t("brokerReviews.modalReview.thanks") }}
                  </h3>
                  <button
                    type="button"
                    @click.prevent="
                      setReviewModalForm({
                        visibility: false,
                        id: null,
                        type: undefined,
                      })
                    "
                    class="btn"
                  >
                    {{ $t("brokerReviews.modalReview.backToPage") }}
                  </button>
                </fieldset>

                <fieldset
                  name="step1"
                  :class="[
                    {
                      '--done': currentStep > 1,
                      '--current': currentStep === 1,
                    },
                  ]"
                >
                  <div class="step_content">
                    <app-input
                      textarea
                      :label="
                        $t('brokerReviews.modalReview.inputsLabels.reviews') + '*'
                      "
                      v-model="text_review"
                      :error="textReviewErrorFlag"
                      :placeholder="
                        $t('brokerReviews.modalReview.inputsValue.reviews')
                      "
                      :error-message="textReviewError"
                      :max-length-text-review="2000"
                    />
                    <review-ratio
                      @input="setReviewFields"
                      name="impression"
                      :error="$v.impression.$anyError"
                    />

                    <app-input
                      :label="$t('brokerReviews.modalReview.inputsLabels.summary') + '*'"
                      v-model="summary"
                      placeholder="—"
                      :error="summaryErrorFlag"
                      :error-message="summaryError"
                    />
                  </div>
                  <button
                    type="button"
                    class="btn"
                    @click.prevent="validateStep"
                  >
                    {{ $t("brokerReviews.modalReview.nextStep") }}
                  </button>
                  <div class="step_placeholder">
                    <p class="step_counter">
                      {{ $t("brokerReviews.modalReview.step") }} 1
                    </p>
                    <icon-done class="step_icon_done"/>
                    <button
                      class="step_go_back"
                      @click.prevent="currentStep = 1"
                    >
                      <icon-arrow/>
                      {{ $t("brokerReviews.modalReview.backToEdit") }}
                    </button>
                  </div>
                </fieldset>
                <fieldset
                  name="step2"
                  :class="[
                    {
                      '--done': currentStep > 2,
                      '--current': currentStep === 2,
                    },
                  ]"
                >
                  <div class="step_content">
                    <app-input
                      :label="$t('brokerReviews.modalReview.inputsLabels.name') + '*'"
                      v-model="name"
                      placeholder="—"
                      :error="$v.name.$anyError"
                    />
                    <app-input
                      :label="
                        $t('brokerReviews.modalReview.inputsLabels.email') + '*'
                      "
                      type="email"
                      v-model="email"
                      placeholder="—"
                      :error="$v.email.$anyError"
                    />
                    <app-input
                      :label="
                        $t('brokerReviews.modalReview.inputsLabels.accNum')
                      "
                      type="text"
                      v-model="account"
                      placeholder="—"
                      :error="$v.account.$anyError"
                    />
                    <app-input
                      :label="
                        $t('brokerReviews.modalReview.inputsLabels.whereFrom') + '*'
                      "
                      v-model="location"
                      placeholder="—"
                      :error="$v.location.$anyError"
                    />
                  </div>
                  <button
                    type="button"
                    class="btn"
                    @click.prevent="validateStep"
                  >
                    {{ $t("brokerReviews.modalReview.nextStep") }}
                  </button>
                  <div class="step_placeholder">
                    <p class="step_counter">
                      {{ $t("brokerReviews.modalReview.step") }} 2
                    </p>
                    <icon-done class="step_icon_done"/>
                    <button
                      class="step_go_back"
                      @click.prevent="currentStep = 2"
                    >
                      <icon-arrow/>
                      {{ $t("brokerReviews.modalReview.backToEdit") }}
                    </button>
                  </div>
                </fieldset>

                <fieldset
                  name="step3"
                  :class="[
                    {
                      '--done': currentStep > 3,
                      '--current': currentStep === 3,
                    },
                  ]"
                >
                  <div
                    class="step_content"
                    :class="{
                    '--vps': reviewModalForm.type.toLowerCase() === 'vps',
                  }"
                  >
                    <div :class="['app-select__wrap']">
                      <span class="input__label"
                      >{{ experienceLabel + '*' }}
                      </span>
                      <div class="experience">
                        <div class="experience__num">
                          <app-input
                            type="number"
                            :min="1"
                            :max="365"
                            v-model="experienceForexNum"
                            placeholder="—"
                            :blur="setLabel('experienceForexLabel', experienceForexNum, experienceForexLabel)"
                            :error="$v.experienceForexNum.$anyError"
                          />
                        </div>
                        <div class="experience__list">
                          <app-select
                            v-model="experienceForexLabel"
                            :options="experienceForexLabels"
                            :placeholder="$t('brokerReviews.modalReview.inputsValue.experiencePlaceholder')"
                            lightMode
                            :error="$v.experienceForexLabel.$anyError"
                          />
                        </div>
                      </div>
                    </div>

                    <div
                      v-if="reviewModalForm.type.toLowerCase() === 'broker'"
                      :class="['app-select__wrap']"
                    >
                      <span class="input__label">
                        {{
                          $t(
                            "brokerReviews.modalReview.inputsLabels.demoExperience",
                            {name: reviewModalForm.brokerName}
                          )
                        }}
                      </span>
                      <div class="experience">
                        <div class="experience__num">
                          <app-input
                            type="number"
                            :min="1"
                            :max="365"
                            v-model="experienceDemoNum"
                            placeholder="—"
                            :blur="setLabel('experienceDemoLabel', experienceDemoNum, experienceDemoLabel)"
                            :error="$v.experienceDemoNum.$anyError"
                          />
                        </div>
                        <div class="experience__list">
                          <app-select
                            v-model="experienceDemoLabel"
                            :options="experienceDemoLabels"
                            :placeholder="$t('brokerReviews.modalReview.inputsValue.experiencePlaceholder')"
                            lightMode
                          />
                        </div>
                      </div>
                    </div>

                    <div
                      v-if="reviewModalForm.type.toLowerCase() === 'broker'"
                      :class="['app-select__wrap']"
                    >
                      <span class="input__label">
                        {{
                          $t(
                            "brokerReviews.modalReview.inputsLabels.realExperience",
                            {name: reviewModalForm.brokerName}
                          )
                        }}
                      </span>
                      <div class="experience">
                        <div class="experience__num">
                          <app-input
                            type="number"
                            :min="1"
                            :max="365"
                            v-model="experienceRealNum"
                            placeholder="—"
                            :blur="setLabel('experienceRealLabel', experienceRealNum, experienceRealLabel)"
                            :error="$v.experienceRealNum.$anyError"
                          />
                        </div>
                        <div class="experience__list">
                          <app-select
                            v-model="experienceRealLabel"
                            :options="experienceRealLabels"
                            :placeholder="$t('brokerReviews.modalReview.inputsValue.experiencePlaceholder')"
                            lightMode
                          />
                        </div>
                      </div>
                    </div>

                    <app-radio
                      @input="setReviewFields"
                      :options="
                        $t('brokerReviews.modalReview.inputsValue.radioAnswers')
                      "
                      :checked="recommend_broker"
                      name="recommend_broker"
                      :label="
                        recommendLabel
                      "
                      :error="$v.recommend_broker.$anyError"
                    />
                    <app-radio
                      v-if="reviewModalForm.type.toLowerCase() === 'broker'"
                      @input="setReviewFields"
                      :options="
                        $t('brokerReviews.modalReview.inputsValue.radioAnswers')
                      "
                      name="good_condition"
                      :checked="good_condition"
                      :label="
                        $t('brokerReviews.modalReview.inputsLabels.conditions')
                      "
                      :error="$v.good_condition.$anyError"
                    />
                    <app-radio
                      @input="setReviewFields"
                      :options="
                        $t('brokerReviews.modalReview.inputsValue.radioAnswers')
                      "
                      name="good_support"
                      :checked="good_support"
                      :label="
                        $t('brokerReviews.modalReview.inputsLabels.support')
                      "
                      :error="$v.good_support.$anyError"
                    />
                  </div>

                  <button
                    type="submit"
                    class="btn"
                    :disabled="reviewSendingStart"
                    @click.prevent="recaptcha"
                  >
                    {{
                      reviewSendingStart
                        ? $t("brokerReviews.modalReview.loading")
                        : $t("brokerReviews.modalReview.submit")
                    }}
                  </button>
                  <div class="step_placeholder">
                    <p class="step_counter">
                      {{ $t("brokerReviews.modalReview.step") }} 3
                    </p>
                    <icon-done class="step_icon_done"/>
                    <button
                      class="step_go_back"
                      @click.prevent="currentStep = 3"
                    >
                      <icon-arrow/>
                      {{ $t("brokerReviews.modalReview.backToEdit") }}
                    </button>
                  </div>
                </fieldset>
              </div>
            </div>
          </form>

          <div
            class="modal__disclaimer"
            v-html="$t(
              'brokerReviews.modalReview.sideText',
              {
                name: reviewModalForm.brokerName,
                type: $t('brokerReviews.modalReview.type.' + reviewModalForm.type.toLowerCase()),
              }
            )"
          ></div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import {email, required, minLength, maxLength, minValue, maxValue} from "vuelidate/lib/validators";
import {mapMutations, mapState} from "vuex";
import addReviewMutations from "~/apollo/mutations/addReview";
import {declensionOfNumbers} from "@/plugins/filters/filters";

export default {
  name: "submitReview",
  components: {
    IconCross: () => import("@/assets/svg/icon-cross.svg?inline"),
    ReviewRatio: () => import("@/components/form/ReviewRatio"),
    AppInput: () => import("@/components/form/AppInput"),
    AppSelect: () => import("@/components/form/AppSelect"),
    AppRadio: () => import("@/components/form/AppRadio"),
    IconDone: () => import("@/assets/svg/radio-check.svg?inline"),
    IconArrow: () => import("@/assets/svg/icon-arrow.svg?inline"),
  },
  filters: {
    declensionOfNumbers,
  },
  data() {
    return {
      singleBroker: {},
      showSubmitReviewModal: false,
      reviewSendingStart: false,
      currentStep: 1,
      options: [
        {
          value: "yes",
          label: "Yes",
        },
        {
          value: "no",
          label: "No",
        },
        {
          value: "dont_Know",
          label: "Don't know",
        },
      ],
      name: "",
      email: "",
      account: "",
      experienceForexNum: null,
      experienceForexLabel: null,
      experienceDemoNum: null,
      experienceDemoLabel: null,
      experienceRealNum: null,
      experienceRealLabel: null,
      recommend_broker: "dont_Know",
      good_condition: "dont_Know",
      good_support: "dont_Know",
      location: "",
      summary: "",
      impression: "",
      text_review: "",
      recaptchaToken: null,
      text_summary: "",
      apiErrors: {
        text_review: "",
        summary: "",
      }
    };
  },
  computed: {
    ...mapState(["reviewModalForm"]),

    experienceForexLabels() {
      if (!this.experienceForexNum) {
        return [
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days")[0],
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months")[0],
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years")[0]
        ]
      }

      let day = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days"),
        parseInt(this.experienceForexNum),
        this.$i18n.localeProperties.slug
      )
      let months = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months"),
        parseInt(this.experienceForexNum),
        this.$i18n.localeProperties.slug
      )
      let years = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years"),
        parseInt(this.experienceForexNum),
        this.$i18n.localeProperties.slug
      )

      return [day, months, years]
    },

    experienceDemoLabels() {
      if (!this.experienceDemoNum) {
        return [
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days")[0],
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months")[0],
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years")[0]
        ]
      }

      let day = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days"),
        parseInt(this.experienceDemoNum),
        this.$i18n.localeProperties.slug
      )
      let months = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months"),
        parseInt(this.experienceDemoNum),
        this.$i18n.localeProperties.slug
      )
      let years = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years"),
        parseInt(this.experienceDemoNum),
        this.$i18n.localeProperties.slug
      )

      return [day, months, years]
    },

    experienceRealLabels() {
      if (!this.experienceRealNum) {
        return [
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days")[0],
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months")[0],
          this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years")[0]
        ]
      }

      let day = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days"),
        parseInt(this.experienceRealNum),
        this.$i18n.localeProperties.slug
      )
      let months = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months"),
        parseInt(this.experienceRealNum),
        this.$i18n.localeProperties.slug
      )
      let years = declensionOfNumbers(
        this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years"),
        parseInt(this.experienceRealNum),
        this.$i18n.localeProperties.slug
      )

      return [day, months, years]
    },
    textReviewError() {
      if (this.apiErrors.text_review.length) {
        return this.apiErrors.text_review;
      }

      if (this.$v.text_review.$model === '' || this.$v.text_review.minLength === false) {
        return this.$t("brokerReviews.modalReview.inputsLabels.minTextError");
      }

      return false;
    },
    textReviewErrorFlag() {
      if (this.apiErrors.text_review.length > 0) {
        return true;
      }

      return this.$v.text_review.$error;
    },
    summaryErrorFlag() {
      if (this.apiErrors.summary.length > 0) {
        return true;
      }

      return this.$v.summary.$anyError;
    },
    summaryError() {
      if (this.apiErrors.summary.length) {
        return this.apiErrors.summary;
      }

      if (this.$v.summary.$model === '' || this.$v.summary.minLength === false) {
        return this.$t("brokerReviews.modalReview.inputsLabels.minTextError");
      }

      return false;
    },
    recommendLabel() {
      if (this.reviewModalForm.type.toLowerCase() === 'broker') {
        return this.$t('brokerReviews.modalReview.inputsLabels.recommendBroker');
      } else {
        return this.$t('brokerReviews.modalReview.inputsLabels.recommendVps');
      }
    },
    experienceLabel() {
      if (this.reviewModalForm.type.toLowerCase() === 'broker') {
        return this.$t('brokerReviews.modalReview.inputsLabels.forexExperience');
      } else {
        return this.$t('brokerReviews.modalReview.inputsLabels.vpsExperience', {
          name: this.reviewModalForm.brokerName,
        });
      }
    },
  },
  methods: {
    ...mapMutations({
      setReviewModalForm: "SET_REVIEW_MODAL_FORM",
    }),

    setLabel(varName, amount, label) {
      if (!label) return false;

      let list = [];
      let daysIndex = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days").findIndex(i => i === label);
      let monthsIndex = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months").findIndex(i => i === label);
      let yearsIndex = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years").findIndex(i => i === label);

      if (daysIndex !== -1) {
        list = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days");
      } else if (monthsIndex !== -1) {
        list = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months");
      } else if (yearsIndex !== -1) {
        list = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years");
      }

      this[varName] = declensionOfNumbers(
        list,
        parseInt(amount),
        this.$i18n.localeProperties.slug
      )
    },

    async submitReview() {
      if (this.$v.$invalid) {
        this.$v.$touch();
      } else {
        this.$v.$reset();
        this.reviewSendingStart = true;

        const prepareExperienceData = (amount, label) => {
          if (amount === null || label === null) {
            return this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.unset");
          }

          let daysIndex = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.days").findIndex(i => i === label);
          let monthsIndex = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.months").findIndex(i => i === label);
          let yearsIndex = this.$t("brokerReviews.modalReview.inputsValue.experienceLabels.years").findIndex(i => i === label);
          let rightLabel = '';

          if (daysIndex !== -1) {
            rightLabel = (parseInt(amount) > 1) ? 'days' : 'day';
          } else if (monthsIndex !== -1) {
            rightLabel = (parseInt(amount) > 1) ? 'months' : 'month';
          } else if (yearsIndex !== -1) {
            rightLabel = (parseInt(amount) > 1) ? 'years' : 'year';
          }

          return `${amount} ${rightLabel}`;
        }

        try {
          await this.$apollo
            .mutate({
              mutation: addReviewMutations,
              variables: {
                reviewable_id: this.reviewModalForm.id,
                reviewable_type: this.reviewModalForm.type.toUpperCase(),
                language_id: this.$store.getters.getLanguageId(
                  this.$i18n.localeProperties.slug
                ),
                name: this.name,
                email: this.email,
                account: this.account,
                reference: "",
                experience_forex: prepareExperienceData(this.experienceForexNum, this.experienceForexLabel),
                experience_demo: prepareExperienceData(this.experienceDemoNum, this.experienceDemoLabel),
                experience_real: prepareExperienceData(this.experienceRealNum, this.experienceRealLabel),
                recommend_item: this.recommend_broker.toUpperCase(),
                good_conditions: this.good_condition.toUpperCase(),
                good_support: this.good_support.toUpperCase(),
                location: this.location,
                summary: this.summary,
                impression: this.impression.toUpperCase(),
                text_review: this.text_review,
                recaptcha: this.recaptchaToken,
              },
            })
            .then((data) => {
              // console.log(data);
              this.currentStep = 4;
              this.reviewSendingStart = false;
            });
        } catch (e) {
          console.error(e);
          this.reviewSendingStart = false;
        }
      }
    },
    async validateStep() {
      if (this.currentStep === 1) {
        if (await this.validateTextFields()) {
          return;
        }
      }
      if (this.$v["step" + this.currentStep].$invalid) {
        this.$v.$touch();
      } else {
        this.$v.$reset();
        this.currentStep++;
      }
    },
    async validateTextFields() {
      let response = await fetch(`${this.$nuxt.context.env.API_REST}/review/check`, {
        method: "POST",
        headers: { Accept: "application/json", "Content-Type": "application/json" },
        body: JSON.stringify({
          'language': this.$i18n.localeProperties.slug,
          'entity_id': this.reviewModalForm.id,
          'entity_type': this.reviewModalForm.type,
          'text_review': this.text_review,
          'summary': this.summary,
        }),
      });

      let result = await response.json();

      let pending = ["text_review", "summary"];
      if (Object.keys(result.errors).length) {
        for (const idx in result.errors) {
          this.apiErrors[idx] = result.errors[idx].join("\n");
          const index = pending.findIndex((el) => el === idx);
          pending.splice(index, 1);
        }
      }

      for (const idx in pending) {
        this.apiErrors[pending[idx]] = "";
      }

      return Object.keys(result.errors).length !== 0;
    },
    setReviewFields(data) {
      this[data.name] = data.value;
      this.$v[data.name].$model = data.value;
    },
    async recaptcha() {
      try {
        this.recaptchaToken = await this.$recaptcha.execute("login");
        await this.submitReview();
        // send token to server alongside your form data
      } catch (error) {
        console.log("Login error:", error);
      }
    },
  },
  validations: {
    text_review: {required, minLength: minLength(3), maxLength: maxLength(2000)},
    summary: {required, minLength: minLength(3), maxLength: maxLength(120)},
    impression: {required},
    name: {required, minLength: minLength(3), maxLength: maxLength(100)},
    email: {required, email},
    account: {minValue: minValue(1), maxLength: maxLength(100)},
    location: {required, minLength: minLength(3), maxLength: maxLength(100)},
    experienceForexNum: {required, minValue: minValue(1), maxValue: maxValue(365)},
    experienceForexLabel: {required},
    experienceDemoNum: {minValue: minValue(1), maxValue: maxValue(365)},
    experienceRealNum: {minValue: minValue(1), maxValue: maxValue(365)},
    recommend_broker: {required},
    good_condition: {required},
    good_support: {required},
    step1: ["text_review", "impression",
      "summary",
    ],
    step2: ["name", "email", "location", "account"],
    step3: [
      "experienceForexNum",
      "experienceForexLabel",
      "experienceDemoNum",
      "experienceRealNum",
      "recommend_broker",
      "good_condition",
      "good_support",
    ],
  },
  async mounted() {
    try {
      await this.$recaptcha.init();
    } catch (e) {
      console.error(e);
    }
  },
};
</script>
<style lang="stylus">
@import "~/assets/stylus/review-modal.styl"

.experience
  display: flex;

  &__num
    flex-shrink 0
    width: 76px;
    margin-right: 1px;

  &__list
    flex-grow: 1;

  .app-input,
  .app-select
    margin-bottom 0
    @media (min-width: large)
      margin-bottom 20px

</style>
