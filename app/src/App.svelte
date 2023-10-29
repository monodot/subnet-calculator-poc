<script>
  import SubnetCalculator from './lib/SubnetCalculator.svelte'
</script>

<main>
  <div>
    <SubnetCalculator cidr="10.64.0.0/21" minAddresses="20" />
  </div>

</main>

<style>
</style>
