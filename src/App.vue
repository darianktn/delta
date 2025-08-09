<script>
import Highcharts from 'highcharts';

export default {
    name: 'TableWithChart',
    data() {
        return {
            tableData: [
                {
                    indicator: 'Выручка, руб',
                    currentDay: 500521,
                    yesterday: 480521,
                    thisDay: 4805121,
                    change: 4,
                    history: [4200000, 4500000, 4800000, 480521, 500521],
                },
                {
                    indicator: 'Наличные',
                    currentDay: 300000,
                    yesterday: 300000,
                    thisDay: 300000,
                    change: 0,
                    history: [280000, 290000, 295000, 300000, 300000],
                    category: 'payment-method',
                },
                {
                    indicator: 'Безналичный расчет',
                    currentDay: 100000,
                    yesterday: 100000,
                    thisDay: 100000,
                    change: 0,
                    history: [95000, 98000, 99000, 100000, 100000],
                    category: 'payment-method',
                },
                {
                    indicator: 'Кредитные карты',
                    currentDay: 100521,
                    yesterday: 100521,
                    thisDay: 100521,
                    change: 0,
                    history: [98000, 89000, 70000, 11221, 100521],
                    category: 'payment-method',
                },
                {
                    indicator: 'Средний чек, руб',
                    currentDay: 1300,
                    yesterday: 900,
                    thisDay: 900,
                    change: 44,
                    history: [850, 900, 950, 900, 1300],
                },
                {
                    indicator: 'Средний гость, руб',
                    currentDay: 1200,
                    yesterday: 800,
                    thisDay: 800,
                    change: 50,
                    history: [750, 800, 850, 800, 1200],
                },
                {
                    indicator: 'Удаления из чека (после оплаты), руб',
                    currentDay: 1000,
                    yesterday: 1100,
                    thisDay: 900,
                    change: -9,
                    history: [900, 950, 1000, 1100, 1000],
                },
                {
                    indicator: 'Удаления из чека (до оплаты), руб',
                    currentDay: 1300,
                    yesterday: 1300,
                    thisDay: 900,
                    change: 0,
                    history: [900, 1000, 1200, 1300, 1300],
                },
                {
                    indicator: 'Количество чеков',
                    currentDay: 34,
                    yesterday: 36,
                    thisDay: 34,
                    change: -6,
                    history: [32, 33, 35, 36, 34],
                },
                {
                    indicator: 'Количество гостей',
                    currentDay: 34,
                    yesterday: 36,
                    thisDay: 32,
                    change: -6,
                    history: [30, 31, 33, 36, 34],
                },
            ],
            selectedIndicator: null,
            chart: null,
        };
    },
    computed: {
        topRows() {
            return this.tableData.filter(row => row.indicator === 'Выручка, руб');
        },
        bottomRows() {
            return this.tableData.filter(row => row.indicator !== 'Выручка, руб');
        },
    },
    mounted() {
        this.syncColumnWidth();
        this.selectedIndicator = this.tableData[0];
        this.renderChart(this.tableData[0]);
        window.addEventListener('resize', this.handleResize, this.syncColumnWidth);
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.handleResize);
        if (this.chart) {
            this.chart.destroy();
        }
    },
    methods: {
        syncColumnWidth() {
            this.$nextTick(() => {
                const secondTableFirstCol = this.$el.querySelectorAll('.data-table')[1]
                    .querySelector('td:first-child');
                if (secondTableFirstCol) {
                    const width = secondTableFirstCol.offsetWidth + 'px';
                    this.$el.querySelectorAll('.data-table')[0]
                        .querySelectorAll('th:first-child, td:first-child')
                        .forEach(cell => cell.style.width = width);
                }
            });
        },
        formatNumber(num) {
            return num.toLocaleString('ru-RU');
        },
        formatPercentage(change) {
            return change === 0 ? '0%' : `${change > 0 ? '+' : ''}${change}%`;
        },
        getChangeClass(change) {
            return {
                'positive-change': change > 0,
                'negative-change': change < 0,
                'neutral-change': change === 0,
            };
        },
        handleResize() {
            if (this.chart) {
                setTimeout(() => {
                    this.chart.reflow();
                }, 100);
            }
        },
        renderChart(row) {
            const options = {
                chart: {
                    type: 'line',
                    height: 200,
                    backgroundColor: '#ffffff',
                    spacing: [15, 15, 15, 15],
                    style: {
                        fontFamily: '-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif',
                    },
                },
                title: {text: null},
                xAxis: {
                    lineColor: 'black',
                    lineWidth: 1,
                    tickLength: 1,
                    tickColor: 'black',
                    labels: {enabled: false},
                    gridLineWidth: 0,
                    startOnTick: true,
                    endOnTick: true,
                },
                yAxis: {
                    title: {text: null},
                    lineColor: 'black',
                    lineWidth: 1,
                    gridLineWidth: 0,
                    labels: {enabled: false},
                },
                legend: {enabled: false},
                series: [
                    {
                        data: row.history,
                        color: '#10b981',
                        marker: {
                            symbol: 'circle',
                            radius: 5,
                            lineWidth: 2,
                            lineColor: '#ffffff',
                            states: {
                                hover: {
                                    fillColor: '#059669',
                                    lineColor: '#ffffff',
                                    lineWidth: 2,
                                    radius: 6,
                                },
                            },
                        },
                        lineWidth: 3,
                    },
                ],
                credits: {enabled: false},
                plotOptions: {
                    series: {
                        animation: {
                            duration: 1000,
                            easing: 'easeOutQuart',
                        },
                        marker: {enabled: true},
                        states: {hover: {lineWidthPlus: 0}},
                    },
                },
                tooltip: {
                    backgroundColor: '#1f2937',
                    borderRadius: 8,
                    borderWidth: 0,
                    shadow: {
                        color: 'rgba(0, 0, 0, 0.1)',
                        offsetX: 0,
                        offsetY: 2,
                        opacity: 0.1,
                        width: 4,
                    },
                    style: {
                        color: '#ffffff',
                        fontSize: '12px',
                        fontWeight: '500',
                    },
                    formatter: function () {
                        return `<b>${this.y.toLocaleString('ru-RU')}</b>`;
                    },
                },
            };

            if (this.chart) {
                this.chart.destroy();
            }
            this.chart = Highcharts.chart('chart', options);
        },
        showGraph(row) {
            this.selectedIndicator = row;
            this.renderChart(row);
        },
    },
};
</script>

<template>
    <div class="dashboard-container">
        <div class="table-wrapper">
            <table class="data-table">
                <thead>
                <tr>
                    <th class="table-header">Показатель</th>
                    <th class="table-header current-day-header">Текущий день</th>
                    <th class="table-header">Вчера</th>
                    <th class="table-header">Этот день недели</th>
                </tr>
                </thead>
                <tbody>
                <tr
                    v-for="(row, index) in topRows"
                    :key="index"
                    class="data-row"
                    :class="{ 'selected-row': selectedIndicator?.indicator === row.indicator }"
                    @click="showGraph(row)">

                    <td class="indicator-cell">{{ row.indicator }}</td>
                    <td class="data-cell current-day-cell">{{ formatNumber(row.currentDay) }}</td>
                    <td class="data-cell change-cell" :class="getChangeClass(row.change)">
                        <span class="value">{{ formatNumber(row.yesterday) }}</span>
                        <span class="percentage">{{ formatPercentage(row.change) }}</span>
                    </td>
                    <td class="data-cell">{{ formatNumber(row.thisDay) }}</td>
                </tr>
                </tbody>
            </table>
            <div class="chart-container">
                <div id="chart"></div>
            </div>
            <table class="data-table">
                <tbody>
                <tr
                    v-for="(row, index) in bottomRows"
                    :key="index"
                    class="data-row"
                    :class="{
              'selected-row': selectedIndicator?.indicator === row.indicator,
              'payment-method-row': row.category === 'payment-method'}"
                    @click="showGraph(row)">

                    <td class="indicator-cell">{{ row.indicator }}</td>
                    <td class="data-cell current-day-cell">{{ formatNumber(row.currentDay) }}</td>
                    <td class="data-cell change-cell" :class="getChangeClass(row.change)">
                        <span class="value">{{ formatNumber(row.yesterday) }}</span>
                        <span class="percentage">{{ formatPercentage(row.change) }}</span>
                    </td>
                    <td class="data-cell">{{ formatNumber(row.thisDay) }}</td>
                </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<style scoped>
.dashboard-container {
    max-width: 1200px;
    margin: 0 auto;
    font-family: Arial, Helvetica, sans-serif;
    background-color: #fafafa;
}

.table-wrapper {
    background: #fff;
}

.data-table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 2px;
    font-size: 13px;
}

.table-header {
    padding: 12px;
    text-align: center;
    font-weight: 700;
    color: #6c757d;
    font-size: 12px;
    background-color: #f8f9fa;
}

.current-day-header {
    background-color: #edf8ff;
}

.data-row {
    cursor: pointer;
    transition: outline 0.2s ease-in-out;
}

.selected-row {
    outline: 2px solid #2196f3;
}

.indicator-cell {
    text-align: left;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.indicator-cell,
.data-cell {
    padding: 12px;

    color: #798083;
    font-weight: 600;
    background-color: #f8f9fa;
}

.payment-method-row .indicator-cell {
    padding-left: 20px;
}

.data-cell {
    text-align: center;
    font-variant-numeric: tabular-nums;
}

.current-day-cell {
    background-color: #edf8ff;
}

.change-cell {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 8px;
    font-weight: 600;
}

.change-cell .percentage {
    white-space: nowrap;
}

.positive-change {
    background-color: #e8f5e8;
    color: #2e7d32;
}

.negative-change {
    background-color: #ffebee;
    color: #c62828;
}

.neutral-change {
    background-color: #f8f9fa;
    color: #6c757d;
}

.chart-container {
    height: 220px;
    padding: 16px;
    background: #fff;
    border-block: 2px solid #fff;
}

#chart {
    height: 100%;
    width: 100%;
}

@media (max-width: 1024px) {
    .dashboard-container {
        padding: 12px;
    }

    .data-table {
        font-size: 12px;
        border-spacing: 2px;
    }

    .table-header {
        font-size: 11px;
        padding: 10px;
    }

    .indicator-cell,
    .data-cell {
        padding: 10px;
    }

    .chart-container {
        height: 200px;
        padding: 12px;
    }
}

@media (max-width: 768px) {
    .dashboard-container {
        padding: 8px;
    }

    .data-table {
        font-size: 11px;
    }

    .table-header {
        font-size: 10px;
        padding: 8px;
    }

    .indicator-cell,
    .data-cell {
        padding: 8px;
    }

    .change-cell {
        gap: 6px;
    }

    .change-cell .value {
        padding: 8px 0 8px 8px;
    }

    .change-cell .percentage {
        padding: 8px 8px 8px 0;
        font-size: 10px;
    }

    .chart-container {
        height: 180px;
        padding: 8px;
    }
}

@media (max-width: 480px) {
    .dashboard-container {
        padding: 6px;
    }

    .data-table {
        font-size: 10px;
        border-spacing: 1px;
    }

    .table-header {
        font-size: 9px;
        padding: 6px;
    }

    .indicator-cell,
    .data-cell {
        padding: 6px;
    }

    .change-cell {
        flex-direction: column;
        align-items: flex-end;
        gap: 4px;
    }

    .change-cell .value {
        padding: 3px 6px;
        text-align: right;
    }

    .change-cell .percentage {
        padding: 0 6px 3px;
        font-size: 8px;
    }

    .chart-container {
        height: 160px;
        padding: 6px;
    }
}
</style>