<template>
  <div class="dropdown">
    <button
      id="exampleDropdown"
      class="btn btn-secondary dropdown-toggle"
      type="button"
      data-toggle="dropdown"
      aria-expanded="false"
    >
      Choose an option 2
    </button>
    <div
      class="dropdown-menu"
      aria-labelledby="exampleDropdown"
    >
      <a
        class="dropdown-item"
        href="#"
        >Option 1</a
      >
      <a
        class="dropdown-item"
        href="#"
        >Option 2</a
      >
      <a
        class="dropdown-item"
        href="#"
        >Option 3</a
      >
      <a
        class="dropdown-item"
        href="#"
        >Option 4</a
      >
    </div>
  </div>
</template>

<script>
export default {
  name: "SimpleDropdown",
};
</script>
