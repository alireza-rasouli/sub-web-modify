<template>
  <div>
    <el-row style="margin-top: 10px">
      <el-col>
        <el-card>
          <div slot="header">
            <svg-icon class="gayhub" icon-class="github" style="float:left" @click="goToProject" />
            <svg-icon class="dianbao" icon-class="telegram" style="float:left;margin-left: 10px"
              @click="gotoTgChannel" />
            <svg-icon class="youguan" icon-class="youtube" style="float:right;margin-left:10px" @click="gotoYouTuBe" />
            <svg-icon class="channel" icon-class="telegram" style="float:right;margin-left: 10px"
              @click="gotoTgChannel" />
            <div style="text-align:center;font-size:15px">Subscription Converter</div>
          </div>
          <el-container>
            <el-form :model="form" label-width="120px" label-position="left" style="width: 100%">
              <el-form-item label="Source Links:">
                <el-input v-model="form.sourceSubUrl" type="textarea" rows="3"
                  placeholder="Supports various subscription links or single node links. Multiple links per line or separated by |" />
              </el-form-item>
              <el-form-item label="Client Type:">
                <el-select v-model="form.clientType" style="width: 100%">
                  <el-option v-for="(v, k) in options.clientTypes" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="Backend URL:">
                <el-select v-model="form.customBackend" allow-create filterable @change="selectChanged"
                  placeholder="Enter your custom backend URL" style="width: 100%">
                  <el-option v-for="(v, k) in options.customBackend" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="Shortener:">
                <el-select v-model="form.shortType" allow-create filterable placeholder="Select or enter shortener API"
                  style="width: 100%">
                  <el-option v-for="(v, k) in options.shortTypes" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="Remote Config:">
                <el-select v-model="form.remoteConfig" allow-create filterable placeholder="Please select" style="width: 100%">
                  <el-option-group v-for="group in options.remoteConfig" :key="group.label" :label="group.label">
                    <el-option v-for="item in group.options" :key="item.value" :label="item.label"
                      :value="item.value"></el-option>
                  </el-option-group>
                </el-select>
              </el-form-item>
              <el-form-item label-width="0px">
                <el-collapse>
                  <el-collapse-item>
                    <template slot="title">
                      <el-form-item label="Advanced Settings:" style="width: 100%;">
                        <el-button type="limr" style="width: 100%;" icon="el-icon-more-outline">Show / Hide
                        </el-button>
                      </el-form-item>
                    </template>
                    <el-form-item label="Include Nodes:">
                      <el-input v-model="form.includeRemarks" placeholder="Nodes to keep (regex supported)" />
                    </el-form-item>
                    <el-form-item label="Exclude Nodes:">
                      <el-input v-model="form.excludeRemarks" placeholder="Nodes to exclude (regex supported)" />
                    </el-form-item>
                    <el-form-item label="Rename Nodes:">
                      <el-input v-model="form.rename" placeholder="Example: `a@b``1@2`. Use \ to escape |" />
                    </el-form-item>
                    <el-form-item label="Device ID:">
                      <el-input v-model="form.devid" placeholder="Remote device ID for QuantumultX" />
                    </el-form-item>
                    <el-form-item label="Update Interval:">
                      <el-input v-model="form.interval" placeholder="Config update interval (in days)" />
                    </el-form-item>
                    <el-form-item label="Filename:">
                      <el-input v-model="form.filename" placeholder="Returned subscription filename" />
                    </el-form-item>
                    <el-form-item class="eldiy" label-width="0px">
                      <el-row type="flex">
                        <el-col>
                          <el-checkbox v-model="form.nodeList" label="Nodes Only" border></el-checkbox>
                        </el-col>
                        <el-popover placement="bottom" v-model="form.extraset">
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.emoji" label="Emoji"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.insert" label="Insert Default Nodes"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.udp" label="Enable UDP"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.xudp" label="Enable XUDP"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.tfo" label="Enable TFO"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.sort" label="Sort Nodes"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.tpl.clash.doh" label="Clash.DoH"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.appendType" label="Append Node Type"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.tpl.surge.doh" label="Surge.DoH"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.tls13" label="Enable TLS 1.3"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.expand" label="Expand Rules"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.new_name" label="Clash New Field Names"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <el-checkbox v-model="form.scv" label="Skip Cert Verify"></el-checkbox>
                            </el-col>
                            <el-col :span="12">
                              <el-checkbox v-model="form.fdn" label="Filter Invalid Nodes"></el-checkbox>
                            </el-col>
                          </el-row>
                          <el-row :gutter="10">
                            <el-col :span="12">
                              <div style="margin-left: 35%">
                                <el-checkbox v-model="form.tpl.singbox.ipv6" label="Sing-Box IPV6"></el-checkbox>
                              </div>
                            </el-col>
                          </el-row>
                          <el-button slot="reference">More Options</el-button>
                        </el-popover>
                      </el-row>
                    </el-form-item>
                  </el-collapse-item>
                </el-collapse>
              </el-form-item>
              <div style="margin-top: 30px"></div>
              <el-divider content-position="center">
                <el-button type="zhuti" @click="change">
                  <i id="rijian" class="el-icon-sunny"></i>
                  <i id="yejian" class="el-icon-moon"></i>
                </el-button>
              </el-divider>
              <el-form-item label="Custom URL:">
                <el-input class="copy-content" disabled v-model="customSubUrl">
                  <el-button slot="append" v-clipboard:copy="customSubUrl" v-clipboard:success="onCopy" ref="copy-btn"
                    icon="el-icon-document-copy">Copy
                  </el-button>
                </el-input>
              </el-form-item>
              <el-form-item label="Short URL:">
                <el-input class="copy-content" v-model="customShortSubUrl" placeholder="Enter custom suffix and click Generate">
                  <el-button slot="append" v-clipboard:copy="customShortSubUrl" v-clipboard:success="onCopy"
                    ref="copy-btn" icon="el-icon-document-copy">Copy
                  </el-button>
                </el-input>
              </el-form-item>
              <el-form-item label-width="0px" style="margin-top: 40px; text-align: center">
                <el-button style="width: 150px" type="danger" @click="makeUrl"
                  :disabled="form.sourceSubUrl.length === 0 || btnBoolean">Generate Subscription
                </el-button>
                <el-button style="width: 150px" type="danger" @click="makeShortUrl" :loading="loading1"
                  :disabled="customSubUrl.length === 0">Generate Short Link
                </el-button>
              </el-form-item>
              <el-form-item label-width="0px" style="text-align: center">
                <el-button style="width: 150px" type="primary" @click="dialogUploadConfigVisible = true"
                  icon="el-icon-upload" :loading="loading2">Upload Config
                </el-button>
                <el-button style="width: 150px" type="primary" @click="dialogLoadConfigVisible = true"
                  icon="el-icon-copy-document" :loading="loading3">Parse from URL
                </el-button>
              </el-form-item>
              <el-form-item label-width="0px" style="text-align: center">
                <el-button style="width: 250px;" type="success" icon="el-icon-video-play"
                  @click="centerDialogVisible = true">Tutorial Videos
                </el-button>
              </el-form-item>
            </el-form>
          </el-container>
        </el-card>
      </el-col>
    </el-row>
    <el-dialog title="Select Video Tutorial" :visible.sync="centerDialogVisible" :show-close="false" width="40vh" top="30vh"
      center>
      <div label-width="0px" style="text-align: center">
        <el-button style="width: 200px;" type="primary" icon="el-icon-video-play"
          @click="gotoBasicVideo(); centerDialogVisible = false">Basic Tutorial
        </el-button>
      </div>
      <div label-width="0px" style="text-align: center;margin: 3vh 0 2vh">
        <el-button style="width: 200px;" type="danger" icon="el-icon-video-play"
          @click="gotoAdvancedVideo(); centerDialogVisible = false">Advanced Tutorial
        </el-button>
      </div>
      <div label-width="0px" style="text-align: center;margin: 3vh 0 2vh">
        <el-button style="width: 200px;" type="warning" icon="el-icon-download" @click="toolsDown">Proxy Tools Kit
        </el-button>
      </div>
    </el-dialog>
    <el-dialog :visible.sync="dialogUploadConfigVisible" :show-close="false" :close-on-click-modal="false"
      :close-on-press-escape="false" width="80%">
      <el-tabs v-model="activeName" type="card">
        <el-tab-pane label="Remote Config Upload" name="first">
          <el-link type="danger" :href="sampleConfig" style="margin-bottom: 15px" target="_blank" icon="el-icon-info">
            Sample Config
          </el-link>
          <el-form label-position="left">
            <el-form-item prop="uploadConfig">
              <el-input v-model="uploadConfig" type="textarea" :autosize="{ minRows: 15, maxRows: 15 }"
                maxlength="50000" show-word-limit></el-input>
            </el-form-item>
          </el-form>
          <div style="float: right">
            <el-button type="primary" @click="uploadConfig = ''; dialogUploadConfigVisible = false">Cancel</el-button>
            <el-button type="primary" @click="confirmUploadConfig" :disabled="uploadConfig.length === 0">Confirm
            </el-button>
          </div>
        </el-tab-pane>
        <el-tab-pane label="JS Sort Nodes" name="second">
          <el-link type="success" :href="scriptConfig" style="margin-bottom: 15px" target="_blank" icon="el-icon-info">
            Sample Case
          </el-link>
          <el-form label-position="left">
            <el-form-item prop="uploadScript">
              <el-input v-model="uploadScript" placeholder="This feature is temporarily suspended." type="textarea"
                :autosize="{ minRows: 15, maxRows: 15 }" maxlength="50000" show-word-limit></el-input>
            </el-form-item>
          </el-form>
          <div style="float: right">
            <el-button type="primary" @click="uploadScript = ''; dialogUploadConfigVisible = false">Cancel</el-button>
            <el-button type="primary" @click="confirmUploadScript" :disabled="uploadScript.length === 0">Confirm
            </el-button>
          </div>
        </el-tab-pane>
        <el-tab-pane label="JS Filter Nodes" name="third">
          <el-link type="warning" :href="filterConfig" style="margin-bottom: 15px" target="_blank" icon="el-icon-info">
            Sample Case
          </el-link>
          <el-form label-position="left">
            <el-form-item prop="uploadFilter">
              <el-input v-model="uploadFilter" placeholder="This feature is temporarily suspended." type="textarea"
                :autosize="{ minRows: 15, maxRows: 15 }" maxlength="50000" show-word-limit></el-input>
            </el-form-item>
          </el-form>
          <div style="float: right">
            <el-button type="primary" @click="uploadFilter = ''; dialogUploadConfigVisible = false">Cancel</el-button>
            <el-button type="primary" @click="confirmUploadScript" :disabled="uploadFilter.length === 0">Confirm
            </el-button>
          </div>
        </el-tab-pane>
      </el-tabs>
    </el-dialog>
    <el-dialog :visible.sync="dialogLoadConfigVisible" :show-close="false" :close-on-click-modal="false"
      :close-on-press-escape="false" width="80%">
      <div slot="title">
        Extract information from long/short links and fill the form
      </div>
      <el-form label-position="left">
        <el-form-item prop="uploadConfig">
          <el-input v-model="loadConfig" type="textarea" :autosize="{ minRows: 15, maxRows: 15 }" maxlength="5000"
            show-word-limit></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="loadConfig = ''; dialogLoadConfigVisible = false">Cancel</el-button>
        <el-button type="primary" @click="confirmLoadConfig" :disabled="loadConfig.length === 0">Confirm
        </el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
const project = process.env.VUE_APP_PROJECT
const configScriptBackend = process.env.VUE_APP_CONFIG_UPLOAD_BACKEND + '/api.php'
const remoteConfigSample = process.env.VUE_APP_SUBCONVERTER_REMOTE_CONFIG
const scriptConfigSample = process.env.VUE_APP_SCRIPT_CONFIG
const filterConfigSample = process.env.VUE_APP_FILTER_CONFIG
const defaultBackend = process.env.VUE_APP_SUBCONVERTER_DEFAULT_BACKEND
const shortUrlBackend = process.env.VUE_APP_MYURLS_DEFAULT_BACKEND + '/short'
const configUploadBackend = process.env.VUE_APP_CONFIG_UPLOAD_BACKEND + '/sub.php'
const basicVideo = process.env.VUE_APP_BASIC_VIDEO
const advancedVideo = process.env.VUE_APP_ADVANCED_VIDEO
const tgBotLink = process.env.VUE_APP_BOT_LINK
const yglink = process.env.VUE_APP_YOUTUBE_LINK
const bzlink = process.env.VUE_APP_BILIBILI_LINK
const downld = 'http://' + window.location.host + '/download.html'
export default {
  data() {
    return {
      backendVersion: "",
      centerDialogVisible: false,
      activeName: 'first',
      // PC Mode
      isPC: true,
      btnBoolean: false,
      options: {
        clientTypes: {
          Clash: "clash",
          "Surge4/5": "surge&ver=4",
          "Sing-Box": "singbox",
          V2Ray: "v2ray",
          Trojan: "trojan",
          ShadowsocksR: "ssr",
          "Mixed Subscription": "mixed",
          Surfboard: "surfboard",
          Quantumult: "quan",
          "Quantumult X": "quanx",
          Loon: "loon",
          Mellow: "mellow",
          Surge3: "surge&ver=3",
          Surge2: "surge&ver=2",
          ClashR: "clashr",
          "Shadowsocks(SIP002)": "ss",
          "Shadowsocks Android(SIP008)": "sssub",
          ShadowsocksD: "ssd",
          "Auto Detect": "auto",
        },
        shortTypes: {
          "v1.mk": "https://v1.mk/short",
          "d1.mk": "https://d1.mk/short",
          "dlj.tf": "https://dlj.tf/short",
          "suo.yt": "https://suo.yt/short",
        },
        customBackend: {
          "CustomPersonalBackend": "https://subconverter-qvff.onrender.com",
          "CM Load Balanced [vless reality+hy1+hy2]": "https://subapi.cmliussss.net",
          "CM Emergency Backup": "https://subapi.fxxk.dedyn.io",
          "Feiyang Enhanced": "https://url.v1.mk",
          "Feiyang Backup": "https://api.v1.mk",
        },
        backendOptions: [
          { value: "https://subapi.cmliussss.net" },
          { value: "https://subapi.fxxk.dedyn.io" },
          { value: "https://url.v1.mk" },
          { value: "https://api.v1.mk" },
        ],
        remoteConfig: [
              {
            label: "CM Rules",
            options: [
              
              {
                label: "IranC4U",
                value: "https://raw.githubusercontent.com/alireza-rasouli/VPN/refs/heads/main/SUBCONFIG.ini"
              },
            
              {
                label: "CM_Online Default (GitHub)",
                value: "https://raw.githubusercontent.com/cmliu/ACL4SSR/main/Clash/config/ACL4SSR_Online.ini"
              },
              {
                label: "CM_Online_MultiCountry Load Balance (GitHub)",
                value: "https://raw.githubusercontent.com/cmliu/ACL4SSR/main/Clash/config/ACL4SSR_Online_MultiCountry.ini"
              },
              {
                label: "CM_Online_Full Multi-Region (GitHub)",
                value: "https://raw.githubusercontent.com/cmliu/ACL4SSR/main/Clash/config/ACL4SSR_Online_Full.ini"
              }
            ]
          },
          {
            label: "General",
            options: [
              {
                label: "Default",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full_NoAuto.ini"
              },
              {
                label: "Default (Auto Speed Test)",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full_AdblockPlus.ini"
              }
            ]
          },
          {
            label: "ACL Rules",
            options: [
              {
                label: "ACL_Default",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online.ini"
              },
              {
                label: "ACL_No Speed Test",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_NoAuto.ini"
              },
              {
                label: "ACL_Adblock",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_AdblockPlus.ini"
              }
            ]
          }
        ]
      },
      form: {
        sourceSubUrl: "",
        clientType: "",
        customBackend: this.getUrlParam() == "" ? "https://subconverter-qvff.onrender.com" : this.getUrlParam(),
        shortType: "https://v1.mk/short",
        remoteConfig: "https://raw.githubusercontent.com/alireza-rasouli/VPN/refs/heads/main/SUBCONFIG.ini",
        excludeRemarks: "",
        includeRemarks: "",
        filename: "",
        rename: "",
        devid: "",
        interval: "",
        emoji: true,
        nodeList: false,
        extraset: false,
        tls13: false,
        udp: false,
        xudp: false,
        tfo: false,
        sort: false,
        expand: true,
        scv: false,
        fdn: false,
        appendType: false,
        insert: false,
        new_name: true,
        tpl: {
          surge: {
            doh: false 
          },
          clash: {
            doh: false
          },
          singbox: {
            ipv6: false
          }
        }
      },
      loading1: false,
      loading2: false,
      loading3: false,
      customSubUrl: "",
      customShortSubUrl: "",
      dialogUploadConfigVisible: false,
      loadConfig: "",
      dialogLoadConfigVisible: false,
      uploadFilter: "",
      uploadScript: "",
      uploadConfig: "",
      myBot: tgBotLink,
      filterConfig: filterConfigSample,
      scriptConfig: scriptConfigSample,
      sampleConfig: remoteConfigSample
    };
  },
  created() {
    document.title = "Online Subscription Converter";
    this.isPC = this.$getOS().isPc;
  },
  mounted() {
    this.form.clientType = "clash";
    this.getBackendVersion();
    this.anhei();
    let lightMedia = window.matchMedia('(prefers-color-scheme: light)');
    let darkMedia = window.matchMedia('(prefers-color-scheme: dark)');
    let callback = (e) => {
      if (e.matches) {
        this.anhei();
      }
    };
    if (typeof darkMedia.addEventListener === 'function' || typeof lightMedia.addEventListener === 'function') {
      lightMedia.addEventListener('change', callback);
      darkMedia.addEventListener('change', callback);
    }
  },
  methods: {
    selectChanged() {
      this.getBackendVersion();
    },
    getUrlParam() {
      let query = window.location.search.substring(1);
      let vars = query.split('&');
      for (let i = 0; i < vars.length; i++) {
        var pair = vars[i].split('=');
        if (pair[0] == "backend") {
          return decodeURIComponent(pair[1]);
        }
      }
      return "";
    },
    anhei() {
      const getLocalTheme = window.localStorage.getItem("localTheme");
      const lightMode = window.matchMedia && window.matchMedia('(prefers-color-scheme: light)');
      const darkMode = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)');
      if (getLocalTheme) {
        document.getElementsByTagName('body')[0].className = getLocalTheme;
      }
      else if (getLocalTheme == null || getLocalTheme == "undefined" || getLocalTheme == "") {
        if (new Date().getHours() >= 19 || new Date().getHours() < 7) {
          document.getElementsByTagName('body')[0].setAttribute('class', 'dark-mode');
        } else {
          document.getElementsByTagName('body')[0].setAttribute('class', 'light-mode');
        }
        if (lightMode && lightMode.matches) {
          document.getElementsByTagName('body')[0].setAttribute('class', 'light-mode');
        }
        if (darkMode && darkMode.matches) {
          document.getElementsByTagName('body')[0].setAttribute('class', 'dark-mode');
        }
      }
    },
    change() {
      var zhuti = document.getElementsByTagName('body')[0].className;
      if (zhuti === 'light-mode') {
        document.getElementsByTagName('body')[0].setAttribute('class', 'dark-mode');
        window.localStorage.setItem('localTheme', 'dark-mode');
      }
      if (zhuti === 'dark-mode') {
        document.getElementsByTagName('body')[0].setAttribute('class', 'light-mode');
        window.localStorage.setItem('localTheme', 'light-mode');
      }
    },
    onCopy() {
      this.$message.success("Copied to clipboard");
    },
    goToProject() {
      window.open(project);
    },
    gotoTgChannel() {
      window.open(tgBotLink);
    },
    gotoBiliBili() {
      window.open(bzlink);
    },
    gotoYouTuBe() {
      window.open(yglink);
    },
    toolsDown() {
      window.open(downld);
    },
    gotoBasicVideo() {
      this.$alert("Don't forget to follow the channel!", {
        type: "warning",
        confirmButtonText: 'Confirm',
        customClass: 'msgbox',
        showClose: false,
      })
        .then(() => {
          window.open(basicVideo);
        });
    },
    gotoAdvancedVideo() {
      this.$alert("Don't forget to follow the channel!", {
        type: "warning",
        confirmButtonText: 'Confirm',
        customClass: 'msgbox',
        showClose: false,
      })
        .then(() => {
          window.open(advancedVideo);
        });
    },
    makeUrl() {
      if (this.form.sourceSubUrl === "" || this.form.clientType === "") {
        this.$message.error("Subscription link and Client type are required");
        return false;
      }
      let backend =
        this.form.customBackend === ""
          ? defaultBackend
          : this.form.customBackend;
      let sourceSub = this.form.sourceSubUrl;
      sourceSub = sourceSub.replace(/(\n|\r|\n\r)/g, "|");
      this.customSubUrl =
        backend +
        "/sub?target=" +
        this.form.clientType +
        "&url=" +
        encodeURIComponent(sourceSub) +
        "&insert=" +
        this.form.insert;
      if (this.form.remoteConfig !== "") {
        this.customSubUrl +=
          "&config=" + encodeURIComponent(this.form.remoteConfig);
      }
      if (this.form.excludeRemarks !== "") {
        this.customSubUrl +=
          "&exclude=" + encodeURIComponent(this.form.excludeRemarks);
      }
      if (this.form.includeRemarks !== "") {
        this.customSubUrl +=
          "&include=" + encodeURIComponent(this.form.includeRemarks);
      }
      if (this.form.filename !== "") {
        this.customSubUrl +=
          "&filename=" + encodeURIComponent(this.form.filename);
      }
      if (this.form.rename !== "") {
        this.customSubUrl +=
          "&rename=" + encodeURIComponent(this.form.rename);
      }
      if (this.form.interval !== "") {
        this.customSubUrl +=
          "&interval=" + encodeURIComponent(this.form.interval * 86400);
      }
      if (this.form.devid !== "") {
        this.customSubUrl +=
          "&dev_id=" + encodeURIComponent(this.form.devid);
      }
      if (this.form.appendType) {
        this.customSubUrl +=
          "&append_type=" + this.form.appendType.toString();
      }
      if (this.form.tls13) {
        this.customSubUrl +=
          "&tls13=" + this.form.tls13.toString();
      }
      if (this.form.sort) {
        this.customSubUrl +=
          "&sort=" + this.form.sort.toString();
      }
      this.customSubUrl +=
        "&emoji=" +
        this.form.emoji.toString() +
        "&list=" +
        this.form.nodeList.toString() +
        "&xudp=" +
        this.form.xudp.toString() +
        "&udp=" +
        this.form.udp.toString() +
        "&tfo=" +
        this.form.tfo.toString() +
        "&expand=" +
        this.form.expand.toString() +
        "&scv=" +
        this.form.scv.toString() +
        "&fdn=" +
        this.form.fdn.toString();
      if (this.form.clientType.includes("surge")) {
        if (this.form.tpl.surge.doh === true) {
          this.customSubUrl += "&surge.doh=true";
        }
      }
      if (this.form.clientType === "clash") {
        if (this.form.tpl.clash.doh === true) {
          this.customSubUrl += "&clash.doh=true";
        }
        this.customSubUrl += "&new_name=" + this.form.new_name.toString();
      }
      if (this.form.clientType === "singbox") {
        if (this.form.tpl.singbox.ipv6 === true) {
          this.customSubUrl += "&singbox.ipv6=1";
        }
      }
      this.$copyText(this.customSubUrl);
      this.$message.success("Custom subscription copied to clipboard");
    },
    makeShortUrl() {
      let duan =
        this.form.shortType === ""
          ? shortUrlBackend
          : this.form.shortType;
      this.loading1 = true;
      let data = new FormData();
      data.append("longUrl", btoa(this.customSubUrl));
      if (this.customShortSubUrl.trim() != "") {
        data.append("shortKey", this.customShortSubUrl.trim().indexOf("http") < 0 ? this.customShortSubUrl.trim() : "");
      }
      this.$axios
        .post(duan, data, {
          header: {
            "Content-Type": "application/form-data; charset=utf-8"
          }
        })
        .then(res => {
          if (res.data.Code === 1 && res.data.ShortUrl !== "") {
            this.customShortSubUrl = res.data.ShortUrl;
            this.$copyText(res.data.ShortUrl);
            this.$message.success("Short link copied to clipboard");
          } else {
            this.$message.error("Failed to get short link: " + res.data.Message);
          }
        })
        .catch(() => {
          this.$message.error("Failed to get short link");
        })
        .finally(() => {
          this.loading1 = false;
        });
    },
    confirmUploadConfig() {
      this.loading2 = true;
      let data = new FormData();
      data.append("config", encodeURIComponent(this.uploadConfig));
      this.$axios
        .post(configUploadBackend, data, {
          header: {
            "Content-Type": "application/form-data; charset=utf-8"
          }
        })
        .then(res => {
          if (res.data.code === 0 && res.data.data !== "") {
            this.$message.success(
              "Remote config uploaded successfully"
            );
            this.form.remoteConfig = res.data.data;
            this.$copyText(this.form.remoteConfig);
            this.dialogUploadConfigVisible = false;
          } else {
            this.$message.error("Upload failed: " + res.data.msg);
          }
        })
        .catch(() => {
          this.$message.error("Upload failed");
        })
        .finally(() => {
          this.loading2 = false;
        });
    },
    analyzeUrl() {
      if (this.loadConfig.indexOf("target") !== -1) {
        return this.loadConfig;
      } else {
        this.loading3 = true;
        return (async () => {
          try {
            let response = await fetch(this.loadConfig, {
              method: "GET",
              redirect: "follow",
            });
            return response.url;
          } catch (e) {
            this.$message.error("Failed to parse short link: " + e)
          } finally {
            this.loading3 = false;
          }
        })();
      }
    },
    confirmLoadConfig() {
      if (this.loadConfig.trim() === "" || !this.loadConfig.trim().includes("http")) {
        this.$message.error("Invalid subscription link");
        return false;
      }
      (async () => {
        let url
        try {
          url = new URL(await this.analyzeUrl())
        } catch (error) {
          this.$message.error("Please enter a valid address!");
          return;
        }
        this.form.customBackend = url.origin
        let param = new URLSearchParams(url.search);
        if (param.get("target")) {
          let target = param.get("target");
          if (target === 'surge' && param.get("ver")) {
            this.form.clientType = target + "&ver=" + param.get("ver");
          } else if (target === 'surge') {
            this.form.clientType = target + "&ver=4"
          } else {
            this.form.clientType = target;
          }
        }
        if (param.get("url")) {
          this.form.sourceSubUrl = param.get("url");
        }
        if (param.get("insert")) {
          this.form.insert = param.get("insert") === 'true';
        }
        if (param.get("config")) {
          this.form.remoteConfig = param.get("config");
        }
        if (param.get("exclude")) {
          this.form.excludeRemarks = param.get("exclude");
        }
        if (param.get("include")) {
          this.form.includeRemarks = param.get("include");
        }
        if (param.get("filename")) {
          this.form.filename = param.get("filename");
        }
        if (param.get("rename")) {
          this.form.rename = param.get("rename");
        }
        if (param.get("interval")) {
          this.form.interval = Math.ceil(param.get("interval") / 86400);
        }
        if (param.get("dev_id")) {
          this.form.devid = param.get("dev_id");
        }
        if (param.get("append_type")) {
          this.form.appendType = param.get("append_type") === 'true';
        }
        if (param.get("tls13")) {
          this.form.tls13 = param.get("tls13");
        }
        if (param.get("xudp")) {
          this.form.xudp = param.get("xudp") === 'true';
        }
        if (param.get("sort")) {
          this.form.sort = param.get("sort") === 'true';
        }
        if (param.get("emoji")) {
          this.form.emoji = param.get("emoji") === 'true';
        }
        if (param.get("list")) {
          this.form.nodeList = param.get("list") === 'true';
        }
        if (param.get("udp")) {
          this.form.udp = param.get("udp") === 'true';
        }
        if (param.get("tfo")) {
          this.form.tfo = param.get("tfo") === 'true';
        }
        if (param.get("expand")) {
          this.form.expand = param.get("expand") === 'true';
        }
        if (param.get("scv")) {
          this.form.scv = param.get("scv") === 'true';
        }
        if (param.get("fdn")) {
          this.form.fdn = param.get("fdn") === 'true';
        }
        if (param.get("surge.doh")) {
          this.form.tpl.surge.doh = param.get("surge.doh") === 'true';
        }
        if (param.get("clash.doh")) {
          this.form.tpl.clash.doh = param.get("clash.doh") === 'true';
        }
        if (param.get("new_name")) {
          this.form.new_name = param.get("new_name") === 'true';
        }
        if (param.get("singbox.ipv6")) {
          this.form.tpl.singbox.ipv6 = param.get("singbox.ipv6") === '1';
        }
        this.dialogLoadConfigVisible = false;
        this.$message.success("Successfully parsed link information");
      })();
    },
    renderPost() {
      let data = new FormData();
      data.append("target", encodeURIComponent(this.form.clientType));
      data.append("url", encodeURIComponent(this.form.sourceSubUrl));
      data.append("config", encodeURIComponent(this.form.remoteConfig));
      data.append("exclude", encodeURIComponent(this.form.excludeRemarks));
      data.append("include", encodeURIComponent(this.form.includeRemarks));
      data.append("rename", encodeURIComponent(this.form.rename));
      data.append("tls13", encodeURIComponent(this.form.tls13.toString()));
      data.append("xudp", encodeURIComponent(this.form.xudp.toString()));
      data.append("emoji", encodeURIComponent(this.form.emoji.toString()));
      data.append("list", encodeURIComponent(this.form.nodeList.toString()));
      data.append("udp", encodeURIComponent(this.form.udp.toString()));
      data.append("tfo", encodeURIComponent(this.form.tfo.toString()));
      data.append("expand", encodeURIComponent(this.form.expand.toString()));
      data.append("scv", encodeURIComponent(this.form.scv.toString()));
      data.append("fdn", encodeURIComponent(this.form.fdn.toString()));
      data.append("sdoh", encodeURIComponent(this.form.tpl.surge.doh.toString()));
      data.append("cdoh", encodeURIComponent(this.form.tpl.clash.doh.toString()));
      data.append("newname", encodeURIComponent(this.form.new_name.toString()));
      return data;
    },
    confirmUploadScript() {
      if (this.form.sourceSubUrl.trim() === "") {
        this.$message.error("Subscription link cannot be empty");
        return false;
      }
      this.loading2 = true;
      let data = this.renderPost();
      data.append("sortscript", encodeURIComponent(this.uploadScript));
      data.append("filterscript", encodeURIComponent(this.uploadFilter));
      this.$axios
        .post(configScriptBackend, data, {
          header: {
            "Content-Type": "application/form-data; charset=utf-8"
          }
        })
        .then(res => {
          if (res.data.code === 0 && res.data.data !== "") {
            this.$message.success(
              "Custom JS uploaded successfully"
            );
            this.customSubUrl = res.data.data;
            this.$copyText(res.data.data);
            this.dialogUploadConfigVisible = false;
            this.btnBoolean = true;
          } else {
            this.$message.error("Upload failed: " + res.data.msg);
          }
        })
        .catch(() => {
          this.$message.error("Upload failed");
        })
        .finally(() => {
          this.loading2 = false;
        })
    },
    getBackendVersion() {
      this.$axios
        .get(
          this.form.customBackend + "/version"
        )
        .then(res => {
          this.backendVersion = res.data.replace(/backend\n$/gm, "");
          this.backendVersion = this.backendVersion.replace("subconverter", "SubConverter");
          this.$message.success(`Backend Version: ${this.backendVersion}`);
        })
        .catch(() => {
          this.$message.error("Failed to connect to backend!");
        });
    }
  }
};
</script>
