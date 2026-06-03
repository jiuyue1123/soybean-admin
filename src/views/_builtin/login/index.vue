<script setup lang="ts">
import { computed, reactive } from 'vue';
import { useAppStore } from '@/store/modules/app';
import { useThemeStore } from '@/store/modules/theme';
import { useAuthStore } from '@/store/modules/auth';
import { useFormRules, useNaiveForm } from '@/hooks/common/form';
import { $t } from '@/locales';

defineOptions({
  name: 'LoginPage'
});

const appStore = useAppStore();
const themeStore = useThemeStore();
const authStore = useAuthStore();
const { formRef, validate } = useNaiveForm();

interface FormModel {
  userName: string;
  password: string;
}

const model: FormModel = reactive({
  userName: '',
  password: ''
});

const rules = computed<Record<keyof FormModel, App.Global.FormRule[]>>(() => {
  const { formRules } = useFormRules();

  return {
    userName: formRules.userName,
    password: formRules.pwd
  };
});

async function handleSubmit() {
  await validate();
  await authStore.login(model.userName, model.password);
}
</script>

<template>
  <div class="relative h-full flex">
    <!-- Left: Brand Panel -->
    <div class="hidden w-1/2 flex-col items-center justify-center bg-primary relative overflow-hidden lg:flex">
      <div class="absolute -top-40 -right-40 size-120 rounded-full bg-primary-400/20" />
      <div class="absolute -bottom-20 -left-20 size-80 rounded-full bg-primary-600/15" />
      <div class="absolute top-1/3 right-1/4 size-50 rounded-full bg-primary-300/10" />

      <div class="relative z-1 flex flex-col items-center gap-24px">
        <SystemLogo class="size-100px drop-shadow-lg" />
        <div class="text-center">
          <h1 class="text-36px font-700 text-white tracking-wider">
            {{ $t('system.title') }}
          </h1>
          <p class="mt-8px text-16px text-white/70">
            {{ $t('system.desc') }}
          </p>
        </div>
      </div>
    </div>

    <!-- Right: Login Form -->
    <div class="flex-1 flex flex-col items-center justify-center bg-[var(--body-color)] p-24px">
      <div class="mb-32px flex flex-col items-center lg:hidden">
        <SystemLogo class="size-56px" />
        <h2 class="mt-8px text-22px font-600 text-primary">{{ $t('system.title') }}</h2>
      </div>

      <div class="w-full max-w-400px">
        <div class="mb-32px text-center lg:text-left">
          <h2 class="text-28px font-700 text-(--text-color-1)">
            {{ $t('system.title') }}
          </h2>
          <p class="mt-8px text-14px text-(--text-color-3)">
            {{ $t('system.desc') }}
          </p>
        </div>

        <NForm
          ref="formRef"
          :model="model"
          :rules="rules"
          size="large"
          :show-label="false"
          class="flex flex-col gap-20px"
          @keyup.enter="handleSubmit"
        >
          <NFormItem path="userName">
            <NInput
              v-model:value="model.userName"
              :placeholder="$t('page.login.common.userNamePlaceholder')"
              :input-props="{ autocomplete: 'username' }"
              class="h-48px!"
            >
              <template #prefix>
                <span class="i-mdi:account-outline text-18px text-(--text-color-3)" />
              </template>
            </NInput>
          </NFormItem>

          <NFormItem path="password">
            <NInput
              v-model:value="model.password"
              type="password"
              show-password-on="click"
              :placeholder="$t('page.login.common.passwordPlaceholder')"
              :input-props="{ autocomplete: 'current-password' }"
              class="h-48px!"
            >
              <template #prefix>
                <span class="i-mdi:lock-outline text-18px text-(--text-color-3)" />
              </template>
            </NInput>
          </NFormItem>

          <NButton
            type="primary"
            size="large"
            block
            :loading="authStore.loginLoading"
            class="h-48px! rounded-8px! text-16px! font-500!"
            @click="handleSubmit"
          >
            {{ $t('common.confirm') }}
          </NButton>
        </NForm>

        <div class="mt-24px flex-center gap-16px">
          <ThemeSchemaSwitch
            :theme-schema="themeStore.themeScheme"
            class="text-20px"
            @switch="themeStore.toggleThemeScheme"
          />
          <div class="h-16px w-1px bg-(--divider-color)" />
          <LangSwitch
            v-if="themeStore.header.multilingual.visible"
            :lang="appStore.locale"
            :lang-options="appStore.localeOptions"
            @change-lang="appStore.changeLocale"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.bg-primary-400\/20 {
  background-color: rgb(var(--primary-400-color) / 0.2);
}
.bg-primary-600\/15 {
  background-color: rgb(var(--primary-600-color) / 0.15);
}
.bg-primary-300\/10 {
  background-color: rgb(var(--primary-300-color) / 0.1);
}
</style>
