<template>
  <div class="dashboard">
    <h1>Expense Dashboard</h1>
    <div class="container">
      <div class="expense-section">
        
        <div class="box">
          <div class="expense-item">
            <h2>This Week's Expenses</h2>
            <p>This Week:</p>
            <p>{{ thisWeekExpense }}</p>
          </div>
        </div>
      </div>
      <div class="expense-section">
        
        <div class="box">
          <div class="expense-item">
            <h2>This Month's Expenses </h2>
            <p>This Month:</p>
            <p>{{ lastMonthExpense }}</p>
          </div>
        </div>
      </div>
      <div class="expense-section">
      
        <div class="box">
          <div class="expense-item">
              <h2>Total Expenses</h2>
            <p>Total:</p>
            <p>{{ totalExpense }}</p>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="expense-chart">
        <h1>Expense Chart</h1>
        <canvas id="expenseChart" width="400" height="200"></canvas>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { Chart, registerables } from 'chart.js';

export default {
  data() {
    return {
      thisWeekExpense: 0,
      lastMonthExpense: 0,
      totalExpense: 0,
    };
  },
  mounted() {
    Chart.register(...registerables);
    this.fetchExpenses();
  },
  methods: {
    fetchExpenses() {
      axios.get('/expenses')
        .then(response => {
          const expensesData = response.data;
          const expenses = expensesData.expenses;
          this.calculateExpenses(expenses);
          this.renderChart();
        })
        .catch(error => {
          console.error('Error fetching expenses:', error);
        });
    },
    calculateExpenses(expenses) {
      if (!Array.isArray(expenses)) {
        console.error('Expenses data is not an array:', expenses);
        return;
      }

      const today = new Date();
      const lastWeek = new Date(today.getTime() - 7 * 24 * 60 * 60 * 1000);
      const lastMonth = new Date(today.getFullYear(), today.getMonth() - 1, today.getDate());

      this.thisWeekExpense = this.calculateTotalForDateRange(expenses, lastWeek, today);
      this.lastMonthExpense = this.calculateTotalForDateRange(expenses, lastMonth, today);
      this.totalExpense = this.calculateTotalForDateRange(expenses, null, today);
    },
    calculateTotalForDateRange(expenses, startDate, endDate) {
      return expenses.reduce((total, expense) => {
        const expenseDate = new Date(expense.expense_date); // Use the correct field name
        if ((!startDate || expenseDate >= startDate) && (!endDate || expenseDate <= endDate)) {
          total += parseFloat(expense.expense_amount); // Make sure to parse expense amount as a float
        }
        return total;
      }, 0);
    },
    renderChart() {
      const ctx = document.getElementById('expenseChart').getContext('2d');
      new Chart(ctx, {
        type: 'bar',
        data: {
          labels: ['This Week', 'Last Month', 'Total'],
          datasets: [{
            label: 'Total Expenses',
            backgroundColor: ['#f87979', '#79f8b7', '#79aaf8'],
            data: [this.thisWeekExpense, this.lastMonthExpense, this.totalExpense],
          }],
        },
        options: {
          scales: {
            y: {
              beginAtZero: true // This ensures the y-axis starts from zero
            }
          }
        }
      });
    },
  },
};
</script>

<style>
.dashboard {
  font-family: Arial, sans-serif;
}

.container {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

.expense-section {
  flex: 1;
}

.box {
  background-color: #ffffff;
  border: 1px solid #dddddd;
  border-radius: 8px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.box + .box {
  margin-top: 20px;
}

.expense-chart {
  flex: 1;
  padding: 20px;
  background-color: #f0f0f0;
}

.expense-item {
  margin-bottom: 10px;
}

canvas {
  max-width: 70%;
  max-height: 350px;
}
</style>
