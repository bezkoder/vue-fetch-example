<template>
  <div id="app" class="container my-3">
    <h3>Vue Fetch example</h3>

    <div class="card mt-3">
      <div class="card-header">Vue Fetch GET - BezKoder.com</div>
      <div class="card-body">
        <div class="input-group input-group-sm">
          <button class="btn btn-sm btn-primary" @click="getAllData">Get All</button>

          <input type="text" ref="get_id" class="form-control ml-2" placeholder="Id" />
          <div class="input-group-append">
            <button class="btn btn-sm btn-primary" @click="getDataById">Get by Id</button>
          </div>

          <input type="text" ref="get_title" class="form-control ml-2" placeholder="Title" />
          <div class="input-group-append">
            <button class="btn btn-sm btn-primary" @click="getDataByTitle">Find By Title</button>
          </div>

          <button class="btn btn-sm btn-warning ml-2" @click="clearGetOutput">Clear</button>
        </div>   
        
        <div v-if="getResult" class="alert alert-secondary mt-2" role="alert"><pre>{{getResult}}</pre></div>
      </div>
    </div>

    <div class="card mt-3">
      <div class="card-header">Vue Fetch POST - BezKoder.com</div>
      <div class="card-body">
        <div class="form-group">
          <input type="text" class="form-control" ref="post_title" placeholder="Title" />
        </div>
        <div class="form-group">
          <input type="text" class="form-control" ref="post_description" placeholder="Description" />
        </div>
        <button class="btn btn-sm btn-primary" @click="postData">Post Data</button>
        <button class="btn btn-sm btn-warning ml-2" @click="clearPostOutput">Clear</button>

        <div v-if="postResult" class="alert alert-secondary mt-2" role="alert"><pre>{{postResult}}</pre></div>
      </div>
    </div>

    <div class="card mt-3">
      <div class="card-header">Vue Fetch PUT - BezKoder.com</div>
      <div class="card-body">
        <div class="form-group">
          <input type="text" class="form-control" ref="put_id" placeholder="Id" />
        </div>
        <div class="form-group">
          <input type="text" class="form-control" ref="put_title" placeholder="Title" />
        </div>
        <div class="form-group">
          <input type="text" class="form-control" ref="put_description" placeholder="Description" />
        </div>
        <div class="form-check mb-2">
          <input type="checkbox" class="form-check-input" ref="put_published" />
          <label class="form-check-label" for="put_published">Publish</label>
        </div>
        <button class="btn btn-sm btn-primary" @click="putData">Update Data</button>
        <button class="btn btn-sm btn-warning ml-2" @click="clearPutOutput">Clear</button>

        <div v-if="putResult" class="alert alert-secondary mt-2" role="alert"><pre>{{putResult}}</pre></div>
      </div>
    </div>

    <div class="card mt-3">
      <div class="card-header">Vue Fetch DELETE - BezKoder.com</div>
      <div class="card-body">
        <div class="input-group input-group-sm">
          <button class="btn btn-sm btn-danger" @click="deleteAllData">Delete All</button>

          <input type="text" ref="delete_id" class="form-control ml-2" placeholder="Id" />
          <div class="input-group-append">
            <button class="btn btn-sm btn-danger" @click="deleteDataById">Delete by Id</button>
          </div>

          <button class="btn btn-sm btn-warning ml-2" @click="clearDeleteOutput">Clear</button>
        </div>    
        
        <div v-if="deleteResult" class="alert alert-secondary mt-2" role="alert"><pre>{{deleteResult}}</pre></div>      
      </div>
    </div>
 
  </div>
</template>

<script>
const baseURL = "http://localhost:8080/api";

export default {
  name: "App",
  data() {
    return {
      getResult: null,
      postResult: null,
      putResult: null,
      deleteResult: null,
    }
  },
  methods: {
    fortmatResponse(res) {
      return JSON.stringify(res, null, 2);
    },

    async getAllData() {
      try {
        const res = await fetch(`${baseURL}/tutorials`);

        if (!res.ok) {
          const message = `An error has occured: ${res.status} - ${res.statusText}`;
          throw new Error(message);
        }

        const data = await res.json();

        const result = {
          status: res.status + "-" + res.statusText,
          headers: {
            "Content-Type": res.headers.get("Content-Type"),
            "Content-Length": res.headers.get("Content-Length"),
          },
          length: res.headers.get("Content-Length"),
          data: data,
        };

        this.getResult = this.fortmatResponse(result);
      } catch (err) {
        this.getResult = err.message;
      }
    },

    async getDataById() {
      const id = this.$refs.get_id.value;

      if (id) {
        try {
          const res = await fetch(`${baseURL}/tutorials/${id}`);

          if (!res.ok) {
            const message = `An error has occured: ${res.status} - ${res.statusText}`;
            throw new Error(message);
          }

          const data = await res.json();

          const result = {
            data: data,
            status: res.status,
            statusText: res.statusText,
            headers: {
              "Content-Type": res.headers.get("Content-Type"),
              "Content-Length": res.headers.get("Content-Length"),
            },
          };

          this.getResult = this.fortmatResponse(result);
        } catch (err) {
          this.getResult = err.message;
        }
      }
    },

    async getDataByTitle() {
      const title = this.$refs.get_title.value;

      if (title) {
        try {
          // const res = await fetch(`${baseURL}/tutorials?title=${title}`);

          let url = new URL(`${baseURL}/tutorials`);
          const params = { title: title };
          url.search = new URLSearchParams(params);

          const res = await fetch(url);

          if (!res.ok) {
            const message = `An error has occured: ${res.status} - ${res.statusText}`;
            throw new Error(message);
          }

          const data = await res.json();

          const result = {
            status: res.status + "-" + res.statusText,
            headers: {
              "Content-Type": res.headers.get("Content-Type"),
              "Content-Length": res.headers.get("Content-Length"),
            },
            data: data,
          };

          this.getResult = this.fortmatResponse(result);
        } catch (err) {
          this.getResult = err.message;
        }
      }
    },

    async postData() {
      const postData = {
        title: this.$refs.post_title.value,
        description: this.$refs.post_description.value,
      };

      try {
        const res = await fetch(`${baseURL}/tutorials`, {
          method: "post",
          headers: {
            "Content-Type": "application/json",
            "x-access-token": "token-value",
          },
          body: JSON.stringify(postData),
        });

        if (!res.ok) {
          const message = `An error has occured: ${res.status} - ${res.statusText}`;
          throw new Error(message);
        }

        const data = await res.json();

        const result = {
          status: res.status + "-" + res.statusText,
          headers: {
            "Content-Type": res.headers.get("Content-Type"),
            "Content-Length": res.headers.get("Content-Length"),
          },
          data: data,
        };

        this.postResult = this.fortmatResponse(result);
      } catch (err) {
        this.postResult = err.message;
      }
    },

    async putData() {
      const { put_id: id, put_title, put_description, put_published } = this.$refs;

      if (id) {
        const putData = {
          title: put_title.value,
          description: put_description.value,
          published: put_published.checked,
        };

        try {
          const res = await fetch(`${baseURL}/tutorials/${id}`, {
            method: "put",
            headers: {
              "Content-Type": "application/json",
              "x-access-token": "token-value",
            },
            body: JSON.stringify(putData),
          });

          if (!res.ok) {
            const message = `An error has occured: ${res.status} - ${res.statusText}`;
            throw new Error(message);
          }

          const data = await res.json();

          const result = {
            status: res.status + "-" + res.statusText,
            headers: { "Content-Type": res.headers.get("Content-Type") },
            data: data,
          };

          this.putResult = this.fortmatResponse(result);
        } catch (err) {
          this.putResult = err.message;
        }
      }
    },

    async deleteAllData() {
      try {
        const res = await fetch(`${baseURL}/tutorials`, { method: "delete" });

        const data = await res.json();

        const result = {
          status: res.status + "-" + res.statusText,
          headers: { "Content-Type": res.headers.get("Content-Type") },
          data: data,
        };

        this.deleteResult = this.fortmatResponse(result);
      } catch (err) {
        this.deleteResult = err.message;
      }
    },

    async deleteDataById() {
      const id = this.$refs.delete_id.value;

      if (id){
        try {
          const res = await fetch(`${baseURL}/tutorials/${id}`, { method: "delete" });

          const data = await res.json();

          const result = {
            status: res.status + "-" + res.statusText,
            headers: { "Content-Type": res.headers.get("Content-Type") },
            data: data,
          };

          this.deleteResult = this.fortmatResponse(result);
        } catch (err) {
          this.deleteResult = err.message;
        }
      }
    },

    clearGetOutput() {
      this.getResult = null;
    },

    clearPostOutput() {
      this.postResult = null;
    },

    clearPutOutput() {
      this.putResult = null;
    },

    clearDeleteOutput() {
      this.deleteResult = null;
    }
  }
}
</script>

<style>
#app {
  max-width: 600px;
  margin: auto;
}
</style>
