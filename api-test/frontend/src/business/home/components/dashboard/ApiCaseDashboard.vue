<template>
  <div class="dashboard-card">
    <el-card shadow="never" class="box-card" style="height: 100%">
      <div slot="header" class="clearfix">
        <span class="dashboard-title">{{ $t('home.dashboard.api_case.title') }}</span>
      </div>
      <div v-loading="loading" element-loading-background="#FFFFFF">
        <div
          v-show="loadError"
          style="
            width: 100%;
            height: 300px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
          ">
          <img style="height: 100px; width: 100px" src="/assets/module/figma/icon_load_error.svg" />
          <span class="addition-info-title" style="color: #646a73">{{ $t('home.dashboard.public.load_error') }}</span>
        </div>
        <div v-show="!loadError">
          <div class="main-info">
            <el-row :gutter="16">
              <el-col :span="12">
                <main-info-card
                  :title="$t('home.dashboard.api_case.title')"
                  :count-data="apiCaseData"
                  :is-execute-info="false"
                  redirect-page-name="api"
                  redirect-data-type="apiTestCase"
                  :link-permission="['PROJECT_API_DEFINITION:READ']"
                  @redirectPage="redirectPage" />
              </el-col>
              <el-col :span="12" v-loading="coveredLoading">
                <main-info-card
                  :title="$t('home.dashboard.public.executed_times_in_week')"
                  :count-data="apiCaseData"
                  :is-execute-info="true" />
              </el-col>
            </el-row>
          </div>
          <div class="addition-info">
            <el-row :gutter="16" style="margin: 0" v-loading="coveredLoading">
              <!--接口覆盖率-->
              <el-col :span="8" style="padding-left: 0">
                <hover-card
                  :title="$t('home.dashboard.api_case.covered_rate')"
                  :main-info="apiCaseData.apiCoveredRate"
                  :tool-tip="apiCoveredRateToolTip">
                  <!--未覆盖、已覆盖-->
                  <template v-slot:mouseOut>
                    <div style="margin: 16px 0px 0px 16px">
                      <el-row>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.covered') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'api', 'coveredTestCase', null)">
                              {{ formatAmount(apiCaseData.coveredCount) }}
                            </el-link>
                          </div>
                        </el-col>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.not_covered') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'api', 'notCoveredTestCase', null)">
                              {{ formatAmount(apiCaseData.notCoveredCount) }}
                            </el-link>
                          </div>
                        </el-col>
                      </el-row>
                    </div>
                  </template>
                </hover-card>
              </el-col>
              <!--用例执行率-->
              <el-col :span="8">
                <hover-card
                  :title="$t('home.dashboard.api_case.executed_rate')"
                  :main-info="apiCaseData.executedRate"
                  :tool-tip="executeRateToolTip">
                  <!--执行、未执行-->
                  <template v-slot:mouseOut>
                    <div style="margin: 16px 0px 0px 16px">
                      <el-row>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.executed') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'apiTestCase', 'executedCount', null)">
                              {{ formatAmount(apiCaseData.executedCount) }}
                            </el-link>
                          </div>
                        </el-col>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.not_executed') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'apiTestCase', 'unexecuteCount', null)">
                              {{ formatAmount(apiCaseData.notExecutedCount) }}
                            </el-link>
                          </div>
                        </el-col>
                      </el-row>
                    </div>
                  </template>
                </hover-card>
              </el-col>
              <!--用例通过率-->
              <el-col :span="8" style="padding-right: 0">
                <hover-card
                  :title="$t('home.dashboard.api_case.pass_rate')"
                  :main-info="apiCaseData.passRate"
                  :tool-tip="passRateToolTip">
                  <!--通过、未通过-->
                  <template v-slot:mouseOut>
                    <div style="margin: 16px 0px 0px 16px">
                      <el-row>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.pass') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'apiTestCase', 'executionPassCount', null)">
                              {{ formatAmount(apiCaseData.passCount) }}
                            </el-link>
                          </div>
                        </el-col>
                        <el-col :span="12">
                          <span class="addition-info-title">
                            {{ $t('home.dashboard.public.not_pass') }}
                          </span>
                          <div class="common-amount">
                            <el-link
                              class="addition-info-num"
                              v-permission-disable="['PROJECT_API_DEFINITION:READ']"
                              @click="redirectPage('api', 'apiTestCase', 'executionFailedCount', null)">
                              {{ formatAmount(apiCaseData.unPassCount) }}
                            </el-link>
                          </div>
                        </el-col>
                      </el-row>
                    </div>
                  </template>
                </hover-card>
              </el-col>
            </el-row>
          </div>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
import hoverCard from '@/business/home/components/card/HoverCard';
import mainInfoCard from '@/business/home/components/card/MainInfoCard';
import {apiCaseCountByProjectId, apiCaseCoveredByProjectId, formatNumber} from '@/api/home';
import {getCurrentProjectID} from 'metersphere-frontend/src/utils/token';

export default {
  name: 'ApiCaseDashboard',
  components: { hoverCard, mainInfoCard },
  data() {
    return {
      loading: false,
      coveredLoading: false,
      loadError: false,
      apiCoveredRateToolTip: this.$t('api_test.home_page.formula.coverage'),
      executeRateToolTip: this.$t('api_test.home_page.formula.case_execute'),
      passRateToolTip: this.$t('api_test.home_page.formula.api_case_pass'),
      apiCaseData: {
        total: 0,
        createdInWeek: 0,
        executedTimesInWeek: 0,
        executedTimes: 0,
        apiCoveredRate: '0%',
        executedRate: '0%',
        passRate: '0%',
        coveredCount: 0,
        notCoveredCount: 0,
        executedCount: 0,
        notExecutedCount: 0,
        passCount: 0,
        unPassCount: 0,
        fakeErrorCount: 0,
      },
    };
  },
  methods: {
    search(versionId) {
      this.loading = true;
      this.coveredLoading = false;
      this.loadError = false;
      let selectProjectId = getCurrentProjectID();
      apiCaseCountByProjectId(selectProjectId, versionId)
        .then((response) => {
          this.loading = false;
          this.loadError = false;
          this.formatApiCaseData(response.data, false);
        })
        .catch(() => {
          this.loading = false;
          this.loadError = true;
        });
      apiCaseCoveredByProjectId(selectProjectId, versionId)
        .then((response) => {
          this.coveredLoading = false;
          this.formatApiCaseData(response.data, true);
        })
        .catch(() => {
          this.coveredLoading = false;
          this.loadError = true;
        });
    },
    formatApiCaseData(apiCaseResponse, isCovered) {
      if (isCovered) {
        this.apiCaseData.coveredCount = apiCaseResponse.coveredCount;
        this.apiCaseData.notCoveredCount = apiCaseResponse.notCoveredCount;
        this.apiCaseData.executedCount = apiCaseResponse.executedCount;
        this.apiCaseData.notExecutedCount = apiCaseResponse.notExecutedCount;
        this.apiCaseData.passCount = apiCaseResponse.passCount;
        this.apiCaseData.unPassCount = apiCaseResponse.unPassCount;
        this.apiCaseData.fakeErrorCount = apiCaseResponse.fakeErrorCount;
        this.apiCaseData.executedRate = apiCaseResponse.executedRate;
        this.apiCaseData.passRate = apiCaseResponse.passRate;
        this.apiCaseData.apiCoveredRate = apiCaseResponse.apiCoveredRate;
      } else {
        this.apiCaseData.total = apiCaseResponse.total;
        this.apiCaseData.createdInWeek = apiCaseResponse.createdInWeek;
        this.apiCaseData.executedTimesInWeek = apiCaseResponse.executedTimesInWeek;
        this.apiCaseData.executedTimes = apiCaseResponse.executedTimes;
      }
    },
    formatAmount(number) {
      return formatNumber(number);
    },
    redirectPage(redirectPage, dataType, selectRange, selectParam) {
      if (selectRange === 'fakeError') {
        selectRange = 'fakeErrorCount';
      }
      this.$emit('redirectPage', redirectPage, dataType, selectRange, selectParam);
    },
  },
};
</script>

<style scoped></style>
