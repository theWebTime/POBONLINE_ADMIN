<template>
  <div class="footer-wrapper">
    <span class="footer-content">
      &copy; {{ new Date().getFullYear() }} Quotation Hub&nbsp;&nbsp;
      <a
        href="tel:+917862813861"
        class="footer-link"
      >
        Developed By The Web Time IT Solution
      </a>
    </span>
  </div>
</template>

<style scoped>
.footer-wrapper {
  width: 100%;
  padding: 16px 0;
  background: #f5f5f5;
  display: flex;
  justify-content: center;
  align-items: center;
  /* Responsive fixed footer, remove if not needed */
  /* position: fixed; bottom: 0; left: 0; */
}

.footer-content {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
  gap: 8px;
  text-align: center;
}

.footer-link {
  color: #1976d2;
  text-decoration: none;
  font-weight: 500;
  white-space: nowrap;
}

@media (max-width: 600px) {
  .footer-content {
    flex-direction: column;
    gap: 4px;
    font-size: 0.95rem;
  }
}
</style>