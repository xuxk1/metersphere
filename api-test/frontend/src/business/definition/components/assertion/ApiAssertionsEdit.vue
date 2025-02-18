<template>
  <div v-loading="loading">
    <div class="assertion-item-editing regex" v-if="assertions.regex.length > 0">
      <div>{{ $t('api_test.request.assertions.regex') }}</div>
      <div class="regex-item" v-for="(regex, index) in assertions.regex" :key="index">
        <ms-api-assertion-regex
          :is-read-only="isReadOnly"
          :list="assertions.regex"
          :case-enable="caseEnable"
          :regex="regex"
          :edit="true"
          :index="index" />
      </div>
    </div>

    <div class="assertion-item-editing json_path" v-if="assertions.jsonPath.length > 0">
      <div>{{ 'JSONPath' }}</div>
      <div class="regex-item" v-for="(jsonPath, index) in assertions.jsonPath" :key="index">
        <ms-api-assertion-json-path
          :is-read-only="isReadOnly"
          :list="assertions.jsonPath"
          :case-enable="caseEnable"
          :json-path="jsonPath"
          :edit="true"
          :index="index" />
      </div>
    </div>

    <div class="assertion-item-editing x_path" v-if="assertions.xpath2.length > 0">
      <div>
        XPath
        <el-select v-model="assertions.xpathType" size="mini" v-loading="loading" @change="reload"
                   :disabled="caseEnable ">
          <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
        <el-tooltip placement="top">
          <div slot="content">
            {{ $t('api_test.request.assertions.assert_info') }}
          </div>
          <i class="el-icon-question" style="cursor: pointer" />
        </el-tooltip>
      </div>
      <div class="regex-item" v-for="(xPath, index) in assertions.xpath2" :key="index">
        <ms-api-assertion-x-path2
          :is-read-only="isReadOnly"
          :list="assertions.xpath2"
          :case-enable="caseEnable"
          :x-path2="xPath"
          :edit="true"
          :index="index" />
      </div>
    </div>

    <div class="assertion-item-editing jsr223" v-if="assertions.jsr223.length > 0">
      <div>{{ $t('api_test.request.assertions.script') }}</div>
      <div class="regex-item" v-for="(assertion, index) in assertions.jsr223" :key="index">
        <ms-api-assertion-jsr223
          :is-read-only="isReadOnly"
          :list="assertions.jsr223"
          :case-enable="caseEnable"
          :assertion="assertion"
          :edit="true"
          :index="index" />
      </div>
    </div>

    <div class="assertion-item-editing response-time" v-if="isShow">
      <div>{{ $t('api_test.request.assertions.response_time') }}</div>
      <div class="regex-item">
        <ms-api-assertion-duration
          :is-read-only="isReadOnly"
          :case-enable="caseEnable"
          v-model="assertions.duration.value"
          :duration="assertions.duration"
          :edit="true" />
      </div>
    </div>
    <div class="assertion-item-editing response-time" v-if="isDocument">
      <div>
        <el-row :gutter="10" type="flex" justify="space-between" align="middle">
          <el-col> {{ assertions.document.type }}-{{ $t('api_test.definition.request.document_structure') }} </el-col>
          <el-col class="assertion-remove-btn">
            <el-tooltip :content="$t('test_resource_pool.enable_disable')" placement="top">
              <el-switch
                v-model="assertions.document.enable"
                class="enable-switch"
                size="mini"
                :disabled="(isReadOnly && !assertions.document.label) || caseEnable"
                style="width: 30px; margin-right: 10px" />
            </el-tooltip>
            <el-tooltip effect="dark" :content="$t('commons.remove')" placement="top-start">
              <el-button
                icon="el-icon-delete"
                type="danger"
                size="mini"
                circle
                @click="remove()"
                :disabled="(isReadOnly && !assertions.document.label && !assertions.root) || caseEnable" />
            </el-tooltip>
          </el-col>
        </el-row>
      </div>
      <ms-document-body :document="assertions.document" :apiId="apiId" :isReadOnly="isReadOnly && !assertions.document.label || caseEnable" @remove="remove" />
    </div>
  </div>
</template>

<script>
import MsApiAssertionRegex from './ApiAssertionRegex';
import MsApiAssertionDuration from './ApiAssertionDuration';
import MsApiAssertionJsonPath from './ApiAssertionJsonPath';
import MsApiAssertionJsr223 from './ApiAssertionJsr223';
import MsApiAssertionXPath2 from './ApiAssertionXPath2';

export default {
  name: 'MsApiAssertionsEdit',

  components: {
    MsApiAssertionXPath2,
    MsApiAssertionJsr223,
    MsApiAssertionJsonPath,
    MsApiAssertionDuration,
    MsApiAssertionRegex,
    MsDocumentBody: () => import('./document/DocumentBody'),
  },

  props: {
    assertions: {},
    reloadData: String,
    apiId: String,
    isReadOnly: {
      type: Boolean,
      default: false,
    },
    caseEnable: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      loading: false,
      options: [
        { value: 'html', label: 'html' },
        { value: 'xml', label: 'xml' },
      ],
    };
  },
  computed: {
    isShow() {
      let rt = this.assertions.duration;
      return rt.value && rt.value !== 0;
    },
    isDocument() {
      return (
        this.assertions.document &&
        this.assertions.document.data &&
        (this.assertions.document.data.json.length > 0 || this.assertions.document.data.xml.length > 0)
      );
    },
  },
  watch: {
    reloadData() {
      this.reload();
    },
  },
  methods: {
    remove() {
      this.assertions.document = {
        type: 'JSON',
        data: { xmlFollowAPI: false, jsonFollowAPI: false, json: [], xml: [] },
        enable: true,
      };
    },
    reload() {
      this.loading = true;
      this.$nextTick(() => {
        this.loading = false;
      });
    },
  },
};
</script>

<style scoped>
.assertion-item-editing {
  padding-left: 10px;
  margin-top: 10px;
}

.assertion-item-editing.regex {
  border-left: 2px solid #7b0274;
}

.assertion-item-editing.json_path {
  border-left: 2px solid #44b3d2;
}

.assertion-item-editing.response-time {
  border-left: 2px solid #dd0240;
}

.assertion-item-editing.jsr223 {
  border-left: 2px solid #1fdd02;
}

.assertion-item-editing.x_path {
  border-left: 2px solid #fca130;
}

.regex-item {
  margin-top: 10px;
}

.assertion-remove-btn {
  text-align: center;
  width: 80px;
}
</style>
