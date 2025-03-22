
#### Problem Statement
You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.
You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return _the maximum profit you can achieve from this transaction_. If you cannot achieve any profit, return `0`.

#### Approach
This is an introductory dynamic programming question. The intuition is to use bottom up DP. That means that you assume that bigger problems require navigating their smaller sub-children. In this case, each profit calculated represents the solution to a smaller subproblem. The brute force for this solution is to calculate the profit for every possible buy and sell date where the sell date is past the buy date (j > i). 
