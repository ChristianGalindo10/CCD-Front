<template>
  <div>
    <!-- <Tutorial /> -->
    <div class="jumbotron fluid" id="indexJumbotron">
      <div class="row">
        <div class="col-5">
          <div>
            <h1 class="display-4">Consorcio de Comidas a Domicilio</h1>
            <p class="lead">Lorem ipsum dolor sit amet consectetur adipisicing elit. Voluptatibus error unde, nulla
              incidunt facilis non consequuntur eligendi accusamus nemo architecto molestias dolores odio perspiciatis
              velit sapiente similique sit asperiores rerum.</p>
          </div>

        </div>
        <div class="col-7" id="header_img"></div>
      </div>
    </div>
    <div id="carouselContainer">
      <div id="carousel-wrapper" v-bind:class="{ anim_next: animNext, anim_previous: animPrev }">
        <div id="menu" :style="menuStyle">
          <div id="current-option">
            <span id="current-option-text1" :data-previous-text="data_previous_text" :data-next-text="data_next_text">{{
                currentOptionText1
            }}</span>
            <span id="current-option-text2" :data-previous-text="data_previous_text2"
              :data-next-text="data_next_text2">{{ currentOptionText2 }}</span>
          </div>
          <button id="previous-option" @click="next"></button>
          <button id="next-option" @click="prev"></button>

        </div>
        <div id="image" :style="imgStyle"></div>
      </div>
    </div>
    <!-- <div style="padding: 10px">
      <h1>Nuxt.js + Spring Boot</h1>
      <p>Message from API: {{ ip }}</p>
    </div> -->
  </div>
</template>

<script>
/**export default {
  name: 'IndexPage'
}**/

export default {
  mounted() {
    console.log(localStorage.getItem('token'))
    console.log(localStorage.getItem('authorities'))
    if (localStorage.getItem('token')!=null) {
      this.$axios.defaults.headers.common['Authorization'] = 'Bearer ' + localStorage.getItem('token');
    }
    /*if(localStorage.getItem('authorities')=='Usuario'){
      alert("Debe iniciar sesión!");
      this.$router.push('/log_in')
    }*/
    const ip = this.$axios.$get('http://localhost:8080/usuarios/get');
    console.log(ip);
    return { ip }
  },
  data() {
    return {
      slide: 0,
      sliding: null,
      i: 0,
      text1_options: [
        "Recomendado1",
        "Recomendado2",
        "Recomendado3",
        "Recomendado4",
        "Recomendado5",
        "Recomendado6",
        "Recomendado7",
        "Recomendado8"
      ],
      text2_options: [
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit."
      ],
      color_options: ["#EBB9D2", "#FE9968", "#7FE0EB", "#6CE5B1","#EBB9D2", "#FE9968", "#7FE0EB", "#6CE5B1"],
      image_options: [
        "img/ca1.jpg",
        "img/ca2.jpg",
        "img/ca3.jpg",
        "img/ca4.jpg",
        "img/ca5.jpg",
        "img/ca6.jpg",
        "img/ca7.jpg",
        "img/ca8.jpg"
      ],
      data_previous_text: "",
      data_next_text: "",
      data_previous_text2: "",
      data_next_text2: "",
      animNext: false,
      animPrev: false,
      backgroundImage: "url(" + "img/ca1.jpg" + ")",
      currentOptionText1: "Recomendado1",
      currentOptionText2: "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla laoreet arcu dignissim urna suscipit interdum. Quisque odio sapien, ultricies sit amet massa vel, pharetra bibendum est. Nunc egestas euismod odio non feugiat. Phasellus blandit euismod nisi a lobortis. Donec pharetra risus ut volutpat efficitur. Morbi pellentesque enim non facilisis blandit.",
    }
  },
  computed: {
    menuStyle() {
      return {
        backgroundColor: this.color_options[this.i]
      }
    },
    imgStyle() {
      return {
        backgroundImage: this.backgroundImage
      }
    }
  },
  methods: {
    onSlideStart(slide) {
      this.sliding = true
    },
    onSlideEnd(slide) {
      this.sliding = false
    },
    next(event) {
      this.i = this.i + 1;
      this.i = this.i % this.text1_options.length;
      this.data_next_text = this.text1_options[this.i];

      this.data_next_text2 = this.text2_options[this.i];

      //mainMenu.style.background = color_options[i];

      //carousel.classList.add("anim-next");
      this.animNext = true;

      setTimeout(() => {
        this.backgroundImage = "url(" + this.image_options[this.i] + ")";
        //this.imgStyle;
      }, 455);

      setTimeout(() => {
        this.currentOptionText1 = this.text1_options[this.i];
        this.currentOptionText2 = this.text2_options[this.i];
        this.animNext = false;
      }, 650);
    },
    prev() {
      if (this.i === 0) {
        this.i = this.text1_options.length;
      }
      this.i = this.i - 1;
      this.data_previous_text = this.text1_options[this.i];

      this.data_previous_text2 = this.text2_options[this.i];

      //mainMenu.style.background = color_options[i];
      //carousel.classList.add("anim-previous");
      this.animPrev = true;

      setTimeout(() => {
        this.backgroundImage = "url(" + this.image_options[this.i] + ")";
        //this.imgStyle;
      }, 455);

      setTimeout(() => {
        this.currentOptionText1 = this.text1_options[this.i];
        this.currentOptionText2 = this.text2_options[this.i];
        this.animPrev = false;
      }, 650);
    }
  },
  async asyncData({ app }) {
    
  }
}
</script>
