<script>
  import Netmask from "netmask";

  export let cidr;
  export let minAddresses;

  $: block = block_info(cidr);
  $: subnets = calc_subnets(cidr, minAddresses);

  function block_info(cidr) {
    try {
      const block = new Netmask.Netmask(cidr);
      return {
        base: block.base,
        broadcast: block.broadcast,
        size: block.size,
        mask: block.mask,
        bitmask: block.bitmask,
      };
    } catch (e) {
      return {
        base: "",
        broadcast: "",
        size: 0,
        mask: "",
        bitmask: "",
      };
    }
  }

  function calc_subnets(cidr, min_addr) {
    const SUBNET_MAX_BITS = 32;

    try {
      const block = new Netmask.Netmask(cidr);
      const min_bits = Math.ceil(Math.log2(min_addr));
      const mask = SUBNET_MAX_BITS - min_bits;
      const max_addr = Math.pow(2, min_bits);
      const count = Math.floor(block.size / max_addr);

      return {
        count: count,
        mask: mask,
        max_addr: max_addr,
      };
    } catch (e) {
      console.log(e);
      return {
        count: 0,
        mask: "",
        max_addr: 0,
      };
    }
  }

</script>

<div id="panel">
  <div class="row">
    <p class="label">
      <label for="cidr"
        ><abbr title="Classless Inter-Domain Routing">CIDR</abbr> Block ([ip]/[bits
        in subnet mask])</label
      >
    </p>
    <div>
      <input id="cidr" type="text" name="cidr" bind:value={cidr} />
    </div>
  </div>
  <p class="label">
    <label for="subnets">Minimum IP addresses required in each subnet</label>
  </p>
  <div>
    <input
      id="minAddresses"
      type="text"
      name="minAddresses"
      bind:value={minAddresses}
    />
  </div>

  <div class="calc-divider"></div>
  <div class="row">
    <p class="label">Number of addresses in this CIDR block</p>
    <p class="calculated">{block.size.toLocaleString()}</p>
  </div>
  <div class="row">
    <p class="label">Start and end IP addresses in this CIDR block</p>
    <p class="calculated">{block.base} &minus; {block.broadcast}</p>
  </div>

  <div class="row">
    <p class="label">
      Smallest subnet mask which will provide at least the IP addresses
      required:
    </p>
    <p class="calculated">/{subnets.mask}</p>
  </div>
  <div class="row">
    <p class="label">Addresses in each new subnet:</p>
    <p class="calculated">{subnets.max_addr.toLocaleString()}</p>
  </div>
  <div class="row">
    <p class="label">
      Number of subnets that can be created within the CIDR block which will have the required number of addresses:
    </p>
    <p class="calculated">{subnets.count.toLocaleString()}</p>
  </div>
</div>

<style>
  div#panel {
    font-family: Arial, Helvetica, sans-serif;
  }
  div.row {
    margin: 1rem 0;
  }
  p.label {
    font-size: 0.9rem;
    margin: 0 0 0.25rem 0;
  }
  p.calculated {
    font-size: 1.25rem;
    font-weight: bold;
    margin: 0;
  }
  input[type="text"] {
    font-size: 1.25rem;
    padding: 0.25rem;
    border: 2px solid #999999;
  }
  input#minAddresses {
    width: 5rem;
  }
  div.calc-divider {
    border-top: 1px solid #999999;
    margin: 1rem 0;
  }
</style>
