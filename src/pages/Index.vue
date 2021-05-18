<template>
  <Layout>
    <!-- Page Header -->
    <header 
    class="masthead" 
    :style="{
      backgroundImage:`url(${GRIDSOME_API_URL+general.cover.url}`
      }
      "
    >
      <div class="overlay"></div>
      <div class="container">
        <div class="row">
          <div class="col-lg-8 col-md-10 mx-auto">
            <div class="site-heading">
              <h1>{{general.title}}</h1>
              <span class="subheading">{{general.subtitle}}</span>
            </div>
          </div>
        </div>
      </div>
      <marquee direction="right" scrolldelay="60" class="position-absolute">
        <a href="https://guide.streetvoice.com/" target="_blank" data-ga-on="click" data-ga-event-category="click" data-ga-event-action="cat run">
            <img src="https://akstatic.streetvoice.com/asset/images/sv-cat.gif" width="20" height="28" border="0">
        </a>
    </marquee>
    </header>

    <!-- Main Content -->
    <div class="container">
      <div class="row">
        <div class="col-lg-8 col-md-10 mx-auto">
          <div
            class="post-preview"
            v-for="edge in $page.posts.edges"
            :key="edge.node.id"
          >
            <g-link :to="'/post/' + edge.node.id">
              <h2 class="post-title">
                {{ edge.node.title }}
              </h2>
              <!-- <h3 class="post-subtitle">
                Problems look mighty small from 150 miles up
              </h3> -->
            </g-link>
            <p class="post-meta">
              <!-- Posted by
              <a href="#">Start Bootstrap</a> -->
              on {{ edge.node.created_at }}
            </p>
            <p>
              <span
                v-for="tag in edge.node.tags"
                :key="tag.id"
                class="tag"
                >
                <g-link :to="'/tag/'+tag.id" >{{ tag.title }}</g-link>
                </span>
            </p>
            <hr />
          </div>
          <!-- Pager -->
          <!-- <div class="clearfix">
            <a class="btn btn-primary float-right" href="#"
              >Older Posts &rarr;</a
            >
          </div> -->
          <Pager :info="$page.posts.pageInfo" />
        </div>
      </div>
    </div>
  </Layout>
</template>

<page-query>
query ($page:Int) {
  posts:allStrapiPost(perPage:10,page:$page) @paginate{
    pageInfo {
      totalPages
      currentPage
    }
    edges{
      node{
        id
        title
        created_at
        content
        tags{
          id
          title
        }
      }
    }
  }
  general:allStrapiGeneral{
    edges{
      node{
        title
        subtitle
        cover{
          url
        }
      }
    }
  }
}
</page-query>

<script>
import { Pager } from "gridsome";
export default {
  metaInfo: {
    title: "Hello, world!",
  },
  components: {
    Pager,
  },
  computed:{
    general(){
      return this.$page.general.edges[0].node
    }
  }
};
</script>

<style lang="scss" scoped>
.post-preview {
  .tag {
    padding-right: 20px;
  }
}
nav[role="navigation"] {
  margin-top: 70px;
  a {
    padding-right: 20px;
  }
}
</style>

