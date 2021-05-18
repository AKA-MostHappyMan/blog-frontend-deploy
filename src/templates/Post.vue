<template>
  <Layout>
    <!-- Page Header -->
    <header
      class="masthead"
      :style="{
        backgroundImage: !$page.post.cover
          ? ''
          : `url(${GRIDSOME_API_URL + $page.post.cover.url})`,
      }"
    >
      <div class="overlay"></div>
      <div class="container">
        <div class="row">
          <div class="col-lg-8 col-md-10 mx-auto">
            <div class="post-heading">
              <h1>{{ $page.post.title }}</h1>
            </div>
          </div>
        </div>
      </div>
    </header>

    <!-- Post Content -->
    <article class="postContent">
      <div class="container">
        <div class="row">
          <div class="music-container" id="music-container">
            <div class="music-info">
              <!-- <h4 id="title">{{ $page.post.title }}</h4> -->
              <div class="progress-container" id="progress-container">
                <div class="progress" id="progress"></div>
              </div>
            </div>

            <audio ref="music" id="audio">
              <source :src="musicUrl" />
            </audio>

            <div class="img-container">
              <img ref="musicImg" alt="music-cover" id="cover" />
            </div>
            <div class="navigation">
              <button id="prev" class="action-btn">
                <i class="fas fa-backward"></i>
              </button>
              <button id="play" class="action-btn action-btn-big">
                <i class="fas fa-play"></i>
              </button>
              <button id="next" class="action-btn">
                <i class="fas fa-forward"></i>
              </button>
            </div>
          </div>

          <div
            class="col-lg-8 col-md-10"
            v-html="mdToHtml($page.post.content)"
          ></div>
        </div>
      </div>
    </article>
  </Layout>
</template>

<page-query>
query($id:ID!){
  post : strapiPost(id:$id){
    id
    title
    content
    music{
      name
      url
    }
    cover{
      url
    }
    tags{
      id
      title
      cover{
        url
      }
    }
    
  }
}
</page-query>

<script>
import MarkdownIt from "markdown-it";
const md = new MarkdownIt();
export default {
  name: "PostPage",
  data() {
    return {
      musicUrl: "",
    };
  },
  mounted() {
    this.getUrl();
    const audio = document.getElementById("audio");
    const musicContainer = document.getElementById("music-container");
    const playBtn = document.getElementById("play");
    const progress = document.getElementById("progress");
    const progressContainer = document.getElementById("progress-container");
    const currTime = document.querySelector("#currTime");
    const durTime = document.querySelector("#durTime");
    function playSong() {
      musicContainer.classList.add("play");
      playBtn.querySelector("i.fas").classList.remove("fa-play");
      playBtn.querySelector("i.fas").classList.add("fa-pause");

      audio.play();
    }
    // Pause song
    function pauseSong() {
      musicContainer.classList.remove("play");
      playBtn.querySelector("i.fas").classList.add("fa-play");
      playBtn.querySelector("i.fas").classList.remove("fa-pause");

      audio.pause();
    }
    // Event listeners
    playBtn.addEventListener("click", () => {
      const isPlaying = musicContainer.classList.contains("play");

      if (isPlaying) {
        pauseSong();
      } else {
        playSong();
      }
    });
    // Update progress bar
    function updateProgress(e) {
      const { duration, currentTime } = e.srcElement;
      const progressPercent = (currentTime / duration) * 100;
      progress.style.width = `${progressPercent}%`;
    }

    // Set progress bar
    function setProgress(e) {
      const width = this.clientWidth;
      const clickX = e.offsetX;
      const duration = audio.duration;

      audio.currentTime = (clickX / width) * duration;
    }

    //get duration & currentTime for Time of song
    function DurTime(e) {
      const { duration, currentTime } = e.srcElement;
      var sec;
      var sec_d;

      // define minutes currentTime
      let min = currentTime == null ? 0 : Math.floor(currentTime / 60);
      min = min < 10 ? "0" + min : min;

      // define seconds currentTime
      function get_sec(x) {
        if (Math.floor(x) >= 60) {
          for (var i = 1; i <= 60; i++) {
            if (Math.floor(x) >= 60 * i && Math.floor(x) < 60 * (i + 1)) {
              sec = Math.floor(x) - 60 * i;
              sec = sec < 10 ? "0" + sec : sec;
            }
          }
        } else {
          sec = Math.floor(x);
          sec = sec < 10 ? "0" + sec : sec;
        }
      }

      get_sec(currentTime, sec);

      // change currentTime DOM
      currTime.innerHTML = min + ":" + sec;

      // define minutes duration
      let min_d = isNaN(duration) === true ? "0" : Math.floor(duration / 60);
      min_d = min_d < 10 ? "0" + min_d : min_d;

      function get_sec_d(x) {
        if (Math.floor(x) >= 60) {
          for (var i = 1; i <= 60; i++) {
            if (Math.floor(x) >= 60 * i && Math.floor(x) < 60 * (i + 1)) {
              sec_d = Math.floor(x) - 60 * i;
              sec_d = sec_d < 10 ? "0" + sec_d : sec_d;
            }
          }
        } else {
          sec_d = isNaN(duration) === true ? "0" : Math.floor(x);
          sec_d = sec_d < 10 ? "0" + sec_d : sec_d;
        }
      }

      // define seconds duration

      get_sec_d(duration);

      // change duration DOM
      durTime.innerHTML = min_d + ":" + sec_d;
    }
    // Time/song update
    audio.addEventListener("timeupdate", updateProgress);

    // Click on progress bar
    progressContainer.addEventListener("click", setProgress);
  },
  methods: {
    mdToHtml(markdown) {
      return md.render(markdown);
    },
    getUrl() {
      let musicUrl = this.GRIDSOME_API_URL + this.$page.post.music.url;
      this.$refs.music.src = musicUrl;
      let musicImg = this.GRIDSOME_API_URL + this.$page.post.tags[0].cover.url;
      this.$refs.musicImg.src = musicImg;
    },
  },
  computed: {},
  watch: {},
};
</script>
<style scoped>
.postContent {
  display: flex;
  justify-content: center;
}
article .container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-family: "Lato", sans-serif;
  margin: 0;
}
.music-container {
  background-color: #fff;
  border-radius: 15px;
  box-shadow: 0 20px 20px 0 rgba(20, 20, 55, 0.6);
  display: flex;
  padding: 20px 30px;
  position: relative;
  margin: 100px 0;
  z-index: 10;
}

.img-container {
  position: relative;
  width: 110px;
}

.img-container::after {
  content: "";
  background-color: #fff;
  border-radius: 50%;
  position: absolute;
  bottom: 50%;
  left: 50%;
  width: 20px;
  height: 20px;
  transform: translate(-50%, -50%);
}

.img-container img {
  border-radius: 50%;
  object-fit: cover;
  height: 110px;
  width: inherit;
  position: absolute;
  bottom: 0;
  left: 0;
  animation: rotate 3s linear infinite;

  animation-play-state: paused;
}

.music-container.play .img-container img {
  animation-play-state: running;
}

@keyframes rotate {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}

.navigation {
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1;
}

.action-btn {
  background-color: #fff;
  border: 0;
  color: #dfdbdf;
  font-size: 20px;
  cursor: pointer;
  padding: 10px;
  margin: 0 20px;
}

.action-btn.action-btn-big {
  color: #cdc2d0;
  font-size: 30px;
}

.action-btn:focus {
  outline: 0;
}

.music-info {
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 15px 15px 0 0;
  position: absolute;
  top: 0;
  left: 20px;
  width: calc(100% - 40px);
  padding: 10px 10px 10px 150px;
  opacity: 0;
  transform: translateY(0%);
  transition: transform 0.3s ease-in, opacity 0.3s ease-in;
  z-index: 0;
}

.music-container.play .music-info {
  opacity: 1;
  transform: translateY(-100%);
}

.music-info h4 {
  margin: 0;
}

.progress-container {
  background: #fff;
  border-radius: 5px;
  cursor: pointer;
  margin: 10px 0;
  height: 4px;
  width: 100%;
}

.progress {
  background-color: #fe8daa;
  border-radius: 5px;
  height: 100%;
  width: 0%;
  transition: width 0.1s linear;
}
</style>