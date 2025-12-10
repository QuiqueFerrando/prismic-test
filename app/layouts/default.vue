<template>
  <div class="text-slate-800">
    <!-- TODO: Remove the following element once you have read the documentation. -->
    <DevOnly>
      <div
        :style="{
          background: '#5163ba',
          padding: '1rem',
          textAlign: 'center',
          fontSize: '0.85rem',
          color: '#fff',
        }"
      ></div>
    </DevOnly>
    <Header />
    <slot />
  </div>
</template>
