<template>
  <div class="flex items-center justify-center">
    <el-card class="w-[500px]">
      <h1>login</h1>
      <el-form
        ref="ruleFormRef"
        :model="ruleForm"
        :rules="rules"
        size="large"
        label-position="top"
        status-icon
        @submit.prevent="submitForm(ruleFormRef)"
      >
        <el-form-item label="Name" prop="username">
          <el-input v-model="ruleForm.username" />
        </el-form-item>
        <el-form-item label="Password" prop="password">
          <el-input v-model="ruleForm.password" type="password" show-password />
        </el-form-item>
        <el-form-item>
          <el-button
            :loading="loading"
            class="w-full"
            type="success"
            native-type="submit"
          >
            Login
          </el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script setup lang="ts">
import type { FormInstance, FormRules } from "element-plus";
import { ref, reactive } from "vue";
import { ElMessage } from "element-plus";
import { useRouter } from "vue-router";
import { useStoreUser } from "../store/user.ts";

interface RuleForm {
  username: string;
  password: string;
}

const router = useRouter();
const userStore = useStoreUser();
const loading = ref(false);
const ruleFormRef = ref<FormInstance>();
const ruleForm = reactive<RuleForm>({
  username: "mor_2314",
  password: "83r5^_",
});

const rules = reactive<FormRules<RuleForm>>({
  username: [
    {
      required: true,
      message: "Обязательное поле для заполнения",
      trigger: "blur",
    },
  ],
  password: [
    {
      required: true,
      message: "Обязательное поле для заполнения",
      trigger: "blur",
    },
  ],
});

const submitForm = async (formEl: FormInstance | undefined) => {
  if (!formEl) return;
  await formEl.validate(async (valid) => {
    if (valid) {
      const { username, password } = ruleForm;
      try {
        loading.value = true;
        await userStore.login(username, password);
        await router.push("/products");
      } catch (e) {
        ElMessage({
          message: "Что то пошло не так",
          type: "error",
        });
      } finally {
        loading.value = false;
      }
    }
  });
};
</script>

<style scoped lang="scss" />
