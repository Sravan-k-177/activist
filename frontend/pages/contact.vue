<!-- SPDX-License-Identifier: AGPL-3.0-or-later -->
<template>
  <div class="bg-layer-0">
    <Head>
      <Title>{{ $t("i18n._global.contact") }}</Title>
    </Head>
    <PageDocs
      :imgUrl="BOOTSTRAP_ENVELOPE_URL"
      imgAltText="i18n.pages.contact.contact_img_alt_text"
    >
      <div
        v-if="!emailSent"
        class="items-center space-y-4 text-left md:items-start"
      >
        <h1 class="pb-2 font-bold">
          {{ $t("i18n.pages.contact.header") }}
        </h1>
        <div class="flex flex-row space-x-3 py-2">
          <Icon
            class="mt-[0.125rem] text-link-text"
            :name="IconMap.CIRCLE_INFO"
            size="1.25em"
          />
          <p>
            {{ $t("i18n.pages.contact.subheader_1") }}
            <a
              class="focus-brand link-text items-center"
              href="https://matrix.to/#/#activist_community:matrix.org"
              target="_blank"
            >
              {{ $t("i18n._global.public_matrix_chat_rooms") }}
              <Icon
                :name="IconMap.EXTERNAL_LINK"
                size="1em"
                style="vertical-align: baseline"
              />
            </a>
            !
          </p>
        </div>
        <div class="flex flex-col space-y-4 lg:space-y-6">
          <p>
            {{ $t("i18n.pages.contact.section_1_paragraph_1_1") }}
            <a
              class="focus-brand link-text items-center"
              href="https://matrix.to/#/#activist_community:matrix.org"
            >
              {{ $t("i18n._global.public_matrix_chat_rooms") }}
              <Icon
                :name="IconMap.EXTERNAL_LINK"
                size="1em"
                style="vertical-align: baseline"
              />
            </a>
            {{ $t("i18n.pages.contact.section_1_paragraph_1_3") }}
            <a
              class="focus-brand link-text items-center"
              href="https://github.com/activist-org/activist"
            >
              {{ $t("i18n._global.on_github") }}
              <Icon
                :name="IconMap.EXTERNAL_LINK"
                size="1em"
                style="vertical-align: baseline"
              />
            </a>
            {{ $t("i18n.pages.contact.section_1_paragraph_1_5") }}
          </p>
          <p>
            {{ $t("i18n.pages.contact.section_1_paragraph_2_1") }}
            <a class="focus-brand link-text" href="mailto:team@activist.org">
              team@activist.org
              <Icon
                :name="IconMap.EXTERNAL_LINK"
                size="1em"
                style="vertical-align: baseline"
            /></a>
            .
            {{ $t("i18n.pages.contact.section_1_paragraph_2_2") }}
            <a
              class="focus-brand link-text items-center"
              href="https://github.com/activist-org/activist/blob/main/.github/CODE_OF_CONDUCT.md"
              target="_blank"
            >
              {{ $t("i18n.pages.contact.section_1_paragraph_2_3") }}
              <Icon
                :name="IconMap.EXTERNAL_LINK"
                size="1em"
                style="vertical-align: baseline"
            /></a>
            .
          </p>
        </div>
        <div>
          <form @submit.prevent="sendEmail" class="flex flex-col space-y-4">
            <div class="flex flex-col space-y-2">
              <label
                :class="{
                  'text-action-red': !nameValidated,
                  '': nameValidated,
                }"
                for="name"
                >{{ $t("i18n.pages.contact.name") }}
                <span v-if="!nameValidated">{{
                  $t("i18n.pages.contact.error_empty")
                }}</span>
              </label>
              <input
                v-model="name"
                @blur="validateName"
                id="name"
                class="rounded-md bg-highlight p-2 placeholder-distinct-text focus:bg-layer-1"
                :class="{
                  'outline outline-2 outline-action-red': !nameValidated,
                  'outline-none focus:outline-none': nameValidated,
                }"
                :placeholder="$t('i18n.pages.contact.name_placeholder')"
                autocomplete="off"
                spellcheck="false"
              />
            </div>
            <div class="flex flex-col space-y-2">
              <label
                :class="{
                  'text-action-red': !emailValidated,
                  '': emailValidated,
                }"
                for="email"
                >{{ $t("i18n.pages.contact.email_label") }}
                <span v-if="!emailValidated">
                  {{ $t("i18n.pages.contact.valid") }}
                  (example@mail.com).
                </span>
              </label>
              <input
                v-model="email"
                @blur="validateEmail"
                id="email"
                class="rounded-md bg-highlight p-2 placeholder-distinct-text focus:bg-layer-1"
                :class="{
                  'outline outline-2 outline-action-red': !emailValidated,
                  'outline-none focus:outline-none': emailValidated,
                }"
                :placeholder="$t('i18n.pages.contact.email_placeholder')"
                autocomplete="off"
                spellcheck="false"
              />
            </div>
            <div class="flex flex-col space-y-2">
              <label
                :class="{
                  'text-action-red': !subjectValidated,
                  '': subjectValidated,
                }"
                for="subject"
              >
                {{ $t("i18n.pages.contact.subject_label") }}
                <span v-if="!subjectValidated">
                  {{ $t("i18n.pages.contact.error_empty") }}
                </span>
              </label>
              <input
                v-model="subject"
                @blur="validateSubject"
                id="subject"
                class="rounded-md bg-highlight p-2 placeholder-distinct-text focus:bg-layer-1"
                :class="{
                  'outline outline-2 outline-action-red': !subjectValidated,
                  'outline-none focus:outline-none': subjectValidated,
                }"
                :placeholder="$t('i18n.pages.contact.subject_placeholder')"
                autocomplete="off"
                spellcheck="false"
              />
            </div>
            <div class="flex flex-col space-y-2">
              <label
                :class="{
                  'text-action-red': !messageValidated,
                  '': messageValidated,
                }"
                for="message"
              >
                {{ $t("i18n.pages.contact.message_label") }}
                <span v-if="!messageValidated">cannot be empty.</span>
              </label>
              <textarea
                v-model="message"
                @blur="validateMessage"
                id="message"
                class="resize-none rounded-md bg-highlight p-2 placeholder-distinct-text focus:bg-layer-1"
                :class="{
                  'outline outline-2 outline-action-red': !messageValidated,
                  'outline-none focus:outline-none': messageValidated,
                }"
                rows="6"
                :placeholder="$t('i18n.pages.contact.message_placeholder')"
                autocomplete="off"
                spellcheck="false"
              ></textarea>
            </div>
            <div class="flex flex-col space-y-2">
              <FriendlyCaptcha />
            </div>
            <button
              class="focus-brand elem-shadow-sm flex w-fit select-none items-center rounded-md border border-primary-text bg-cta-orange fill-primary-text px-4 py-2 text-center font-semibold dark:border-cta-orange dark:bg-cta-orange/10 dark:fill-cta-orange dark:text-cta-orange xl:rounded-lg"
              :class="{
                'cursor-not-allowed': buttonDisabled,
                'hover:bg-cta-orange/80 active:bg-cta-orange dark:hover:bg-cta-orange/25 dark:active:bg-cta-orange/10':
                  !buttonDisabled,
              }"
              type="submit"
              :disabled="buttonDisabled"
              :aria-label="$t('i18n.pages.contact.send_form_aria_label')"
            >
              {{ $t("i18n.pages.contact.send") }}
            </button>
          </form>
        </div>
      </div>
      <div
        v-else
        class="flex flex-col items-center justify-center space-y-4 pb-8 text-center md:items-start md:space-y-6 md:text-start"
      >
        <h1 class="pb-2 font-bold">
          {{ $t("i18n.pages.contact.thanks_1") }}
        </h1>
        <div class="flex flex-row space-x-3 py-2 text-start">
          <Icon
            class="mt-[0.125rem] text-link-text"
            :name="IconMap.CIRCLE_INFO"
            size="1.25em"
          />
          <p>
            {{ $t("i18n.pages.contact.subheader_1") }}
            <a
              class="focus-brand link-text items-center"
              href="https://matrix.to/#/#activist_community:matrix.org"
              target="_blank"
            >
              {{ $t("i18n._global.public_matrix_chat_rooms") }}
              <Icon
                :name="IconMap.EXTERNAL_LINK"
                size="1em"
                style="vertical-align: baseline"
              />
            </a>
            !
          </p>
        </div>
        <p>
          {{ $t("i18n.pages.contact.thanks_2") }}
        </p>
        <BtnRouteInternal
          :cta="false"
          label="i18n._global.return_home"
          linkTo="/"
          fontSize="lg"
          ariaLabel="i18n._global.return_home_aria_label"
        />
      </div>
    </PageDocs>
  </div>
</template>

<script setup lang="ts">
import { IconMap } from "~/types/icon-map";

const name = ref("");
const email = ref("");
const message = ref("");
const subject = ref("");
const nameValidated = ref(true);
const emailValidated = ref(true);
const subjectValidated = ref(true);
const messageValidated = ref(true);
const buttonDisabled = ref(true);
const mail = useMail();
const emailSent = ref(false);

const validateEmail = () => {
  if (!email.value.match(/.*@\w+\.\w+/)) {
    emailValidated.value = false;
  }
};

const validateSubject = () => {
  if (subject.value.trim().length === 0) {
    subjectValidated.value = false;
  }
};

const validateMessage = () => {
  if (message.value.trim().length === 0) {
    messageValidated.value = false;
  }
};

const validateName = () => {
  if (name.value.trim().length === 0) {
    nameValidated.value = false;
  }
};
watch(name, () => {
  if (name.value.length > 0) {
    nameValidated.value = true;
  }
});

watch(email, () => {
  if (email.value.match(/.*@\w+\.\w+/)) {
    emailValidated.value = true;
  }
});

watch(subject, () => {
  if (subject.value.length > 0) {
    subjectValidated.value = true;
  }
});

watch(message, () => {
  if (message.value.length > 0) {
    messageValidated.value = true;
  }
});

watch([message, subject, email, name], () => {
  if (
    name.value.trim().length > 0 &&
    email.value.match(/.*@\w+\.\w+/) &&
    subject.value.trim().length > 0 &&
    message.value.trim().length > 0
  ) {
    buttonDisabled.value = false;
  } else {
    buttonDisabled.value = true;
  }
});

const sendEmail = async () => {
  if (
    name.value.trim().length > 0 &&
    email.value.match(/.*@\w+\.\w+/) &&
    subject.value.trim().length > 0 &&
    message.value.trim().length > 0
  ) {
    await mail.send({
      from: "contact@activist.org",
      subject: `activist contact form: ${subject.value}`,
      text: message.value,
    });
    emailSent.value = true;
  }
};
</script>
