<template>
    <div>
      跳转中...
    </div>
</template>

<script setup>
useHead({
    title: '跳转中...'
  })
  
  onMounted(() => {
    useRouter().push('/timer');
  });
</script>
