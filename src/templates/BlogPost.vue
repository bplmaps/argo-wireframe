<template>
  <Layout>
    <content-header :image="$page.article.image" :staticImage="false" :opacity="0"></content-header>

    <div class="container sm:pxi-0 mx-auto overflow-x-hidden text-gray-800 dark:text-gray-500">
      <div class="lg:mx-32 md:mx-16 sm:mx-8 mx-4 pt-8">
        <section class="post-header container mx-auto px-0 mb-16 text-center">
          <h1
            class="text-gray-800 dark:text-gray-400 font-extrabold tracking-wider mb-6"
          >{{ $page.article.title}}</h1>
          <span class="tracking-wide text-sm">
            <g-link
              class="font-medium"
              :to="$page.article.category.path"
            >{{ $page.article.category.title }}</g-link>&nbsp;&middot;&nbsp;
            <time :datetime="$page.article.datetime">{{ $page.article.humanTime }}</time>
            &nbsp;&middot;&nbsp;
            {{ $page.article.timeToRead }} min read
          </span>
        </section>
      </div>

      <div class="lg:mx-32 md:mx-16 px-4">
        <section class="post-content container mx-auto relative font-serif">
          <div v-html="$page.article.content"></div>
        </section>

        <section class="post-tags container mx-auto relative py-10">
          <g-link
            v-for="tag in $page.article.tags"
            :key="tag.id"
            :to="tag.path"
            class="text-xs bg-transparent hover:text-blue-700 py-2 px-4 mr-2 border hover:border-blue-500 border-gray-600 text-gray-700 dark:text-gray-400 rounded-full"
          >{{ tag.title }}</g-link>
        </section>
      </div>
    </div>

    <div class="border-t border-b bg-gray-100 dark:border-black dark:bg-gray-900 dark:text-gray-500">
      <div class="container mx-auto">
        <div class="lg:mx-32 md:mx-16 px-4 sm:px-0">
          <section class="container mx-auto py-10">
            <div class="flex flex-wrap justify-center">
              <div class="w-full flex justify-center md:w-10/12 mb-4 text-center">
                <div class="mb-2 sm:mb-0 w-full">
                  <div class="md:flex p-6 pl-0 self-center">
                    <g-image
                      :src="$page.article.author[0].image"
                      class="h-16 w-16 md:h-24 md:w-24 mx-auto md:mx-0 md:mr-6 rounded-full bg-gray-200"
                    ></g-image>

                    <div class="text-center md:text-left">
                      <g-link :to="$page.article.author[0].path" class="text-black dark:text-white">
                        <h2 class="text-lg my-1 mt-2 md:mt-0">{{ $page.article.author[0].name }}</h2>
                      </g-link>
                      <div v-if="authors.length>0" class="post-authors font-light text-sm pt-2">
                        Among with
                        <g-link
                          class="font-normal"
                          :to="author.path"
                          v-for="author in authors"
                          :key="author.name"
                        >{{author.name}}</g-link>
                      </div>
                      <div
                        class="font-light tracking-wider leading-relaxed py-4"
                      >{{ $page.article.author[0].bio }}</div>
                      <div class>
                        <a
                          :href="$page.article.author[0].facebook"
                          target="_blank"
                          rel="noopener noreferrer"
                          class="hover:text-blue-500"
                        >
                          <font-awesome :icon="['fab', 'facebook']" />
                        </a>
                        &nbsp;
                        <a
                          :href="$page.article.author[0].twitter"
                          target="_blank"
                          rel="noopener noreferrer"
                          class="hover:text-blue-500"
                        >
                          <font-awesome :icon="['fab', 'twitter']" />
                        </a>
                        &nbsp;
                        <a
                          :href="$page.article.author[0].linkedin"
                          target="_blank"
                          rel="noopener noreferrer"
                          class="hover:text-blue-500"
                        >
                          <font-awesome :icon="['fab', 'linkedin']" />
                        </a>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </section>
        </div>
      </div>
    </div>

    <section class="post-related pt-10" v-if="relatedRecords.length>0">
      <div class="container mx-auto">
        
        <div class="text-center">
          <h4 class="font-light my-0">Recommended for you</h4>
        </div>
        <div class="flex flex-wrap justify-center pt-8 pb-8">
          
          <CardItem
            :record="relatedRecord.node"
            v-for="relatedRecord in relatedRecords"
            :key="relatedRecord.node.id"
          ></CardItem>
        </div>
      </div>
    </section>
  </Layout>
</template>

<page-query>
  query($recordId: ID!, $tags: [ID]) {
    article(id: $recordId) {
      title
      path
      image(width:1600, height:800)
      image_caption
      excerpt
      content
      humanTime : created(format:"DD MMMM YYYY")
      datetime : created(format:"ddd MMM DD YYYY hh:mm:ss zZ")
      
      timeToRead
      tags {
        id
        title
        path
      }
      category {
        id
        title
        path
        belongsTo(limit:4) {
          totalCount
          edges {
            node {
              ... on Article {
                title
                path
              }
            }
          }
        }
      }
      author {
        id
        name
        image
        path
        bio
      }
    }

    related: allArticle(
      filter: { id: { ne: $recordId }, tags: {containsAny: $tags} }
    ) {
      edges {
        node {
          title
      path
      image(width:1600, height:800)
      image_caption
      excerpt
      content
      humanTime : created(format:"DD MMMM YYYY")
      datetime : created(format:"ddd MMM DD YYYY hh:mm:ss zZ")
      
      timeToRead
      tags {
        id
        title
        path
      }
      category {
        id
        title
        path
        belongsTo(limit:4) {
          totalCount
          edges {
            node {
              ... on Article {
                title
                path
              }
            }
          }
        }
      }
      author {
        id
        name
        image
        path
      }
        }
      }
    }


    
  }
</page-query>

<script>
import CardItem from "~/components/Content/CardItem.vue";
import ContentHeader from "~/components/Partials/ContentHeader.vue";
import mediumZoom from "medium-zoom";
import { sampleSize } from "lodash";

export default {
  components: {
    CardItem,
    ContentHeader
  },
  metaInfo() {
    return {
      title: this.$page.article.title
    };
  },
  computed: {
    relatedRecords() {
      return sampleSize(this.$page.related.edges, 2);
    },
    authors() {
      let authors = [];
      for (let index = 1; index < this.$page.article.author.length; index++) {
        authors.push({
          name: this.$page.article.author[index].name,
          path: this.$page.article.author[index].path
        });
      }

      return authors;
    }
  },
  mounted() {
    mediumZoom(".post-content img");
  }
};
</script>