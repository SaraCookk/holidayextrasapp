<template>
  <b-container>
    <b-row>
      <b-col md="12">
        <b-input-group size="lg" prepend="Search" class="flickr-search">
          <b-form-input v-model="search"></b-form-input>
        </b-input-group>
      </b-col>
    </b-row>
    <b-row>
      <b-card-group columns>
        <b-col v-for="photo in Photos" class="item" md="12">
          <b-card :title="photo.title"
                  :img-src="photo.media.m"
                  img-alt="Image"
                  img-top
                  img-fluid
                  tag="article"
                  style="max-width: 20rem;"
                  class="mb-2">
            <span class="item-date">31 May 2017</span>
            <hr/>
            <p>By <a :href="'https://www.flickr.com/photos/' + photo.author_id" :title="formatAuthor(photo.author)" target="_blank">{{ formatAuthor(photo.author) }}</a></p>
            <ul class="tags">
              <li v-for="tag in splitTags(photo.tags)" class="item-tag">
                <a :href="'https://www.flickr.com/photos/tags/' + tag" target="_blank" class="item-taglink">{{ tag }}</a>
              </li>
            </ul>
          </b-card>
        </b-col>
      </b-card-group>
    </b-row>
  </b-container>
</template>

<script>
    import jsonp from "jsonp";
    import debounce from "debounce";

export default {
    name: 'PhotoFeed',
    data: function () {
        return {
            Photos: [],
            apiURL: "https://api.flickr.com/services/feeds/photos_public.gne?format=json",
            search: ''
        }
    },
    mounted(){
        this.getFlickrFeed();
    },
    methods: {
        getFlickrFeed(){
            let jsonp = require('jsonp');
            let self = this;

            jsonp(this.apiURL, {name: 'jsonFlickrFeed'}, (err, data) => {
                if (err) {
                    console.log(err.message);
                }
                else {
                    self.Photos = data.items;
                }
            })
        },
        formatAuthor(authorString){
            if (authorString) return authorString.split("\"")[1];
            return "Author";
        },
        splitTags(tagsString) {
            if (tagsString) return tagsString.split(" ");
        },
        filteredPhotos(){
            let self = this;
            let apiURL = "https://api.flickr.com/services/feeds/photos_public.gne?tags=" + self.search + "&format=json";
            let jsonp = require('jsonp');

            jsonp(apiURL, {name: 'jsonFlickrFeed'}, (err, data) => {
                if (err) {
                    console.log(err.message);
                }
                else {
                    self.Photos = data.items;
                }
            })
        }
    },
    watch: {
        search() {
            this.filteredPhotos();
        }
    },
    created() {
        var debounce = require('debounce');
        this.filteredPhotos = debounce(this.filteredPhotos, 700);
    }
}
</script>


<style scoped lang="scss">
  $secondary-color: #333;

  .flickr-search {
    margin-bottom: 20px;
  }
  .card-columns {
    max-width: 100%;

    .card {
      width: 100%;
      max-width: none !important;
    }
  }
  .item {
    text-align: left;
    .card-title {
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    }
    .item-date {
      color: $secondary-color;
      font-weight: 600;
      text-transform: uppercase;
    }

    .tags {

      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;

      .item-tag {
        margin-right: .5rem;
        margin-bottom: 0.71429rem !important;
        display: inline-block;

        .item-taglink {
          padding: 0.28571rem 1.07143rem !important;
          color: #555 !important;
          border-radius: 50px !important;
          background-color: #eee !important;
        }
      }
    }
  }
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
}
a {
  color: #42b983;
}
</style>
