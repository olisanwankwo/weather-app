<tempalte>
  <div class="row">
    <div class="label col-2"><label for="name">Name: </label></div>
    <div class="datas col-8">
      <input
        class="input"
        id="name"
        type="text"
        v-model="formdata.name"
        :placeholder="[[userDetails.name]]"
      />
    </div>
  </div>
</tempalte>
<img :src="imageSrc" alt="Image Description">
<img v-bind:src="'http://openweathermap.org/img/wn/' + imageSrc + '.png' "  />
<script>

</script>