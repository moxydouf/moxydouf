<template>
  <footer>
    <!-- External link to the THM website -->
    <a href="https://www.thm.de">THM</a>

    <!-- Router link to the 'about' page -->
    <RouterLink :to="{ name: 'about' }">{{ $t('footer.datenschutz') }}</RouterLink>

    <!-- External link to the Impressum page -->
    <a href="https://www.thm.de/site/impressum.html">{{ $t('footer.impressum') }}</a>
  </footer>
</template>

<script>
/**
 * Footer component that includes links for navigation and external sites.
 * 
 * @component
 */
export default {
  name: 'Footer'
}
</script>

<style scoped>
/* Styles for the footer element */
footer {
  width: 100%; /* Full width of the container */
  bottom: 0; /* Fixed at the bottom of the viewport */
  display: flex; /* Flexbox layout for the footer */
  justify-content: center; /* Center items horizontally */
  align-items: center; /* Center items vertically */
  padding: 10px; /* Padding inside the footer */
  background-color: #f8f9fa; /* Light background color */
}

.dark-mode footer {
  background-color: #333333;
  color: #ffffff;
}

/* Styles for anchor tags within the footer */
footer a {
  color: #9dcc67; /* Text color for links */
  text-decoration: none; /* No underline for links */
  margin-left: 30px; /* Space between links */
}

/* Styles for anchor tags on hover */
footer a:hover {
  color: #007bff; /* Text color on hover */
}


@media (max-width: 1100px) and (max-height: 1400px) {
footer {
  position: fixed;
}
footer a {
  margin-left: auto;
  margin-right: auto;
  font-family: 'Honk', sans-serif;
  font-size: 25px;
  font-weight: bolder;
}
}
</style>
