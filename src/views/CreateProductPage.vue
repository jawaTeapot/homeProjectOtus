<template>
  <div>
    <div class="flex items-center justify-center">
      <h1>Create Product</h1>
    </div>

    <div class="mt-5 flex items-center justify-center">
      <div class="w-[500px]">
        <el-form
          ref="ruleFormRef"
          :model="ruleForm"
          :rules="rules"
          label-width="90px"
          status-icon
          style="width: 100%"
        >
          <div class="rounded mb-5">
            <img class="rounded" :src="ruleForm.image" alt="img" />
          </div>
          <el-form-item label="Название:" prop="title">
            <el-input v-model="ruleForm.title" />
          </el-form-item>
          <el-form-item label="Категория:" prop="category">
            <el-input v-model="ruleForm.category" />
          </el-form-item>
          <el-form-item label="Цена:" prop="price">
            <el-input-number v-model="ruleForm.price" :min="0" />
          </el-form-item>
          <el-form-item label="Описание:" prop="description">
            <el-input
              v-model="ruleForm.description"
              :autosize="{ minRows: 3, maxRows: 4 }"
              type="textarea"
              resize="none"
            />
          </el-form-item>
          <el-form-item>
            <el-button
              :loading="loading"
              type="primary"
              @click="submitForm(ruleFormRef)"
            >
              Создать
            </el-button>
            <el-button @click="resetForm(ruleFormRef)">Очистить</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref } from "vue";
import type { FormInstance, FormRules } from "element-plus";
import { useStoreProduct } from "../store/product.ts";
import { ElMessage } from "element-plus";

interface RuleForm {
  title: string;
  price: number;
  description: string;
  image: string;
  category: string;
}

const projectStore = useStoreProduct();
const loading = ref(false);
const ruleFormRef = ref<FormInstance>();
const ruleForm = reactive<RuleForm>({
  title: "",
  price: 0,
  description: "",
  image: "https://i.pravatar.cc",
  category: "",
});

const rules = reactive<FormRules<RuleForm>>({
  title: [
    { required: true, message: "Please input Activity name", trigger: "blur" },
  ],
  price: [
    {
      required: true,
      message: "Please input Activity surname",
      trigger: "blur",
    },
  ],
  description: [
    {
      required: true,
      message: "Please input Activity description",
      trigger: "blur",
    },
  ],
  category: [
    {
      required: true,
      message: "Please input Activity category",
      trigger: "blur",
    },
  ],
});

const submitForm = async (formEl: FormInstance | undefined) => {
  if (!formEl) return;
  await formEl.validate(async (valid) => {
    if (valid) {
      const { title, price, description, image, category } = ruleForm;
      try {
        loading.value = true;
        await projectStore.createProduct({
          title,
          price,
          description,
          image,
          category,
        });
        formEl.resetFields();
        ElMessage({
          message: "Вы создали новый продукт",
          type: "success",
        });
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

const resetForm = (formEl: FormInstance | undefined) => {
  if (!formEl) return;
  formEl.resetFields();
};
</script>

<style scoped lang="scss" />
