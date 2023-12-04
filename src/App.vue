<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div>
        <v-btn @click="dialogPlaylist = true" color="green">
          Criar Playlist
        </v-btn>
        <v-dialog
          persistent
          fullscreen
          v-model="dialogPlaylist"
          max-width="500px"
        >
          <v-card>
            <v-toolbar style="background-color: #1daf80; color: #fff">
              <v-btn icon dark @click="dialogPlaylist = false">
                <v-icon>mdi-close</v-icon>
              </v-btn>
              <v-toolbar-title>
                <span class="headline">Lista de Playlist</span>
              </v-toolbar-title>
              <v-spacer></v-spacer>
              <v-toolbar-items> </v-toolbar-items>
            </v-toolbar>
            <div class="row justify-content-center mt-5">
              <v-text-field
                v-model="nomePlaylist"
                placeholder="Nome da playlist"
                clear-icon="clear"
                style="margin: 0 5px; padding-top: 5px !important"
                :type="'text'"
                :outlined="false"
              />
              <v-list>
                <v-list-item-group>
                  <v-list-item v-for="album in albuns" :key="album.album_id">
                    <v-list-item-content>
                      <v-list-item-title>{{
                        album.descricao
                      }}</v-list-item-title>
                      <v-list-item-subtitle
                        >R$ {{ album.preco_compra }}</v-list-item-subtitle
                      >

                      <v-list>
                        <v-list-item-group>
                          <v-list-item
                            v-for="faixa in faixasByAlbum(album.album_id)"
                            :key="faixa.faixa_id"
                          >
                            <v-list-item-content
                              @click="faixa.addPlaylist = true"
                            >
                              <v-list-item-title
                                >{{ faixa.descricao }}
                              </v-list-item-title>
                              <v-list-item-subtitle>
                                Número da Faixa
                                {{ faixa.numero_faixa }}
                              </v-list-item-subtitle>
                              <v-list-item-subtitle>
                                Tempo
                                {{ faixa.tempo_execucao }}
                              </v-list-item-subtitle>
                            </v-list-item-content>
                            <v-list-item-action>
                              <v-checkbox
                                v-model="faixa.addPlaylist"
                                color="green"
                              ></v-checkbox>
                            </v-list-item-action>
                          </v-list-item>
                        </v-list-item-group>
                      </v-list>
                    </v-list-item-content>
                    <!-- Botão cadastro playlist -->
                  </v-list-item>
                </v-list-item-group>
                <v-list-item-action>
                  <v-btn color="green" @click="cadastroPlaylist()">
                    Cadastrar
                  </v-btn>
                </v-list-item-action>
              </v-list>
            </div>
          </v-card>
        </v-dialog>
      </div>
      <div>
        <v-btn @click="dialogPlaylistListar = true" color="green" class="mx-1">
          Listar Playlist
        </v-btn>
        <v-dialog
          persistent
          fullscreen
          v-model="dialogPlaylistListar"
          max-width="500px"
        >
          <v-card>
            <v-toolbar style="background-color: #1daf80; color: #fff">
              <v-btn icon dark @click="dialogPlaylistListar = false">
                <v-icon>mdi-close</v-icon>
              </v-btn>
              <v-toolbar-title>
                <span class="headline">Nova Playlist</span>
              </v-toolbar-title>
              <v-spacer></v-spacer>
              <v-toolbar-items> </v-toolbar-items>
            </v-toolbar>
            <div class="row justify-content-center mt-5">
              <v-list>
                <v-list-item-group>
                  <v-list-item
                    v-for="playlist in playlists"
                    :key="playlist.playlist_id"
                  >
                    <v-list-item-content>
                      <v-list-item-title>
                        <h3>{{ playlist.nome }}</h3></v-list-item-title
                      >
                      <!-- <v-list-item-action>
                        <v-btn color="blue"> Adicionar mais músicas </v-btn>
                      </v-list-item-action> -->
                      <v-list-item-subtitle>
                        Tempo
                        {{ playlist.tempo_total_execucao }}
                      </v-list-item-subtitle>
                      <v-list-item-subtitle>
                        Criada em
                        {{ playlist.data_criacao }}
                      </v-list-item-subtitle>

                      <v-list>
                        <v-list-item-group>
                          <v-list-item
                            v-for="faixa in playlist.faixas"
                            :key="faixa.faixa_id"
                          >
                            <v-list-item-content
                              @click="faixa.addPlaylist = true"
                            >
                              <v-list-item-title
                                >{{ faixa.descricao }}
                              </v-list-item-title>
                              <v-list-item-subtitle>
                                Número da Faixa
                                {{ faixa.numero_faixa }}
                              </v-list-item-subtitle>
                              <v-list-item-subtitle>
                                Tempo
                                {{ faixa.tempo_execucao }}
                              </v-list-item-subtitle>
                            </v-list-item-content>
                            <v-list-item-action>
                              <v-btn
                                @click="removerFaixaPlaylist(faixa, playlist)"
                                color="red"
                              >
                                Remover
                              </v-btn>
                            </v-list-item-action>
                          </v-list-item>
                        </v-list-item-group>
                      </v-list>
                    </v-list-item-content>
                  </v-list-item>
                </v-list-item-group>
              </v-list>
            </div>
          </v-card>
        </v-dialog>
      </div>
    </v-app-bar>

    <v-main>
      <v-list>
        <v-list-item-group>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-title
                >Compositor com mais músicas em playlists:
                {{ compositorMaisPlaylists.compositor }}</v-list-item-title
              >
              <v-list-item-subtitle
                >Quantidade de playlists presente:
                {{ compositorMaisPlaylists.quantidade }}</v-list-item-subtitle
              >
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-title
                >Gravadora com mais músicas composta por Dvorack em playlists:
                {{ gravadoraDvorack.gravadora }}</v-list-item-title
              >
              <v-list-item-subtitle
                >Quantidade de playlists presente:
                {{ gravadoraDvorack.quantidade }}</v-list-item-subtitle
              >
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-title
                >Albuns com preço acima da média</v-list-item-title
              >
              <v-list-item-subtitle>Lista de albuns</v-list-item-subtitle>
              <v-list>
                <v-list-item-group>
                  <v-list-item
                    v-for="album in albunsAcimaMedia"
                    :key="album.id"
                  >
                    <v-list-item-content>
                      <v-list-item-title>{{
                        album.descricao
                      }}</v-list-item-title>
                      <v-list-item-subtitle
                        >R$ {{ album.preco_compra }}</v-list-item-subtitle
                      >
                    </v-list-item-content>
                  </v-list-item>
                </v-list-item-group>
              </v-list>
            </v-list-item-content>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-main>
  </v-app>
</template>

<script>
// import HelloWorld from './components/HelloWorld';

export default {
  name: "App",

  components: {
    // HelloWorld,
  },

  data: () => ({
    albuns: [],
    faixas: [],
    playlists: [],
    albunsAcimaMedia: [],
    faixasPlaylists: [],
    compositorMaisPlaylists: {},
    gravadoraDvorack: {},
    dialogPlaylist: false,
    dialogPlaylistListar: false,

    nomePlaylist: "",
  }),
  async mounted() {
    await this.getAlbuns();
    await this.getFaixas();
    await this.getFaixasPlaylists();
    await this.getPlayslists();
    await this.getAlbunsPrecoAcimaMedia();
    await this.getCompositorMaisPlaylists();
    await this.getGravadoraDvorack();
  },
  methods: {
    async getAlbuns() {
      this.albuns = await fetch("http://localhost:8080/albums", {
        method: "GET",
        redirect: "follow",
      }).then((response) => response.json());
    },

    async getPlayslists() {
      const playlists = await fetch("http://localhost:8080/playlists", {
        method: "GET",
        redirect: "follow",
      }).then((response) => response.json());
      this.playlists = playlists.map((playlist) => {
        const date = new Date(playlist.data_criacao);
        const faixas = this.faixasPlaylists
          .filter((fp) => fp.playlist_id === playlist.playlist_id)
          .map((fp) => {
            return this.faixas.find((faixa) => faixa.faixa_id === fp.faixa_id);
          });
        return {
          ...playlist,
          data_criacao: date.toLocaleDateString("pt-BR"),
          faixas,
        };
      });
    },
    async getFaixas() {
      this.faixas = await fetch("http://localhost:8080/faixas", {
        method: "GET",
        redirect: "follow",
      }).then((response) => response.json());
      this.faixas = this.faixas.map((faixa) => {
        return {
          ...faixa,
          addPlaylist: false,
        };
      });
    },
    async getAlbunsPrecoAcimaMedia() {
      this.albunsAcimaMedia = await fetch(
        "http://localhost:8080/albums/above-average-price",
        {
          method: "GET",
          redirect: "follow",
        }
      ).then((response) => response.json());
    },
    async getCompositorMaisPlaylists() {
      this.compositorMaisPlaylists = await fetch(
        "http://localhost:8080/compositor-mais-playlists",
        {
          method: "GET",
          redirect: "follow",
        }
      ).then(async (response) => {
        const res = await response.json();
        const compositor = res[0] ?? [];
        return {
          compositor: compositor[0] ?? "Não há compositor",
          quantidade: compositor[1] ?? 0,
        };
      });
    },
    async getFaixasPlaylists() {
      this.faixasPlaylists = await fetch(
        "http://localhost:8080/faixas-playlists",
        {
          method: "GET",
          redirect: "follow",
        }
      ).then(async (response) => {
        const res = await response.json();
        const fp = res.map((fp) => {
          return {
            playlist_id: fp[0],
            faixa_id: fp[1],
          };
        });
        return fp;
      });
    },
    async getGravadoraDvorack() {
      this.gravadoraDvorack = await fetch(
        "http://localhost:8080/gravadora-mais-faixa-dvorack",
        {
          method: "GET",
          redirect: "follow",
        }
      ).then(async (response) => {
        const res = await response.json();
        const gravadora = res[0] ?? [];
        return {
          gravadora: gravadora[0] ?? "Não há gravadora",
          quantidade: gravadora[1] ?? 0,
        };
      });
    },
    faixasByAlbum(albumId) {
      return this.faixas.filter((faixa) => faixa.album_id === albumId);
    },
    async cadastroPlaylist() {
      const faixasSelecionadas = this.faixas
        .filter((faixa) => faixa.addPlaylist)
        .map((faixa) => faixa.faixa_id);

      await fetch("http://localhost:8080/criar-playlist", {
        method: "POST",
        redirect: "follow",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          faixas: faixasSelecionadas,
          nome: this.nomePlaylist,
        }),
      });
      await this.getFaixasPlaylists();
      await this.getPlayslists();
      this.faixas = this.faixas.map((faixa) => {
        return {
          ...faixa,
          addPlaylist: false,
        };
      });
      this.dialogPlaylist = false;
    },
    async removerFaixaPlaylist(faixa, playlist) {
      await fetch("http://localhost:8080/remover-faixa-playlist", {
        method: "POST",
        redirect: "follow",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          playlist_id: playlist.playlist_id,
          faixa_id: faixa.faixa_id,
        }),
      });
      const faixas = playlist.faixas.filter(
        (f) => f.faixa_id !== faixa.faixa_id
      );
      this.playlists = this.playlists.map((p) => {
        if (p.playlist_id === playlist.playlist_id) {
          return {
            ...p,
            faixas,
          };
        }
        return p;
      });
    },
  },
};
</script>
