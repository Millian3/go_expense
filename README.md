package main

import (
 "encoding/csv"
 "fmt"
 "os"
 "strconv"
 "time"
)

// Expense represents an expense entry
type Expense struct {
 Date     string
 Amount   float64
 Category string
 Note     string
}

const fileName = "expenses.csv"

func main() {
 for {
  fmt.Println("\nExpense Tracker")
  fmt.Println("1. Add Expense")
  fmt.Println("2. View Expenses")
  fmt.Println("3. Search by Category")
  fmt.Println("4. Exit")
  fmt.Print("Choose an option: ")

  var choice int
  fmt.Scan(&choice)

  switch choice {
  case 1:
   addExpense()
  case 2:
   viewExpenses()
  case 3:
   searchByCategory()
  case 4:
   fmt.Println("Exiting...")
   return
  default:
   fmt.Println("Invalid option. Try again.")
  }
 }
}

func addExpense() {
 var amount float64
 var category, note string

 fmt.Print("Enter amount: ")
 fmt.Scan(&amount)

 fmt.Print("Enter category: ")
 fmt.Scan(&category)

 fmt.Print("Enter note (optional): ")
 fmt.Scan(&note)

 expense := Expense{
  Date:     time.Now().Format("2006-01-02"),
  Amount:   amount,
  Category: category,
  Note:     note,
 }

 file, err := os.OpenFile(fileName, os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
 if err != nil {
  fmt.Println("Error opening file:", err)
  return
 }
 defer file.Close()

 writer := csv.NewWriter(file)
 defer writer.Flush()

 err = writer.Write([]string{expense.Date, fmt.Sprintf("%.2f", expense.Amount), expense.Category, expense.Note})
 if err != nil {
  fmt.Println("Error writing to file:", err)
  return
 }

 fmt.Println("Expense added successfully!")
}

func viewExpenses() {
 file, err := os.Open(fileName)
 if err != nil {
  fmt.Println("Error opening file:", err)
  return
 }
 defer file.Close()

 reader := csv.NewReader(file)
 expenses, err := reader.ReadAll()
 if err != nil {
  fmt.Println("Error reading file:", err)
  return
 }

 var total float64
 fmt.Println("\nDate  Amount Category Note")
 for _, record := range expenses {
  amount, _ := strconv.ParseFloat(record[1], 64)
  total += amount
  fmt.Printf("%s %s %s %s\n", record[0], record[1], record[2], record[3])
 }
 fmt.Printf("\nTotal Expenses: %.2f\n", total)
}

func searchByCategory() {
 var searchCategory string
 fmt.Print("Enter category to search: ")
 fmt.Scan(&searchCategory)

 file, err := os.Open(fileName)
 if err != nil {
  fmt.Println("Error opening file:", err)
  return
 }
 defer file.Close()

 reader := csv.NewReader(file)
 expenses, err := reader.ReadAll()
 if err != nil {
  fmt.Println("Error reading file:", err)
  return
 }

 fmt.Println("\nDate  Amount Category Note")
 var total float64
 for _, record := range expenses {
  if record[2] == searchCategory {
   amount, _ := strconv.ParseFloat(record[1], 64)
   total += amount
   fmt.Printf("%s %s %s %s\n", record[0], record[1], record[2], record[3])
  }
 }
 fmt.Printf("\nTotal for '%s': %.2f\n", searchCategory, total)
}
