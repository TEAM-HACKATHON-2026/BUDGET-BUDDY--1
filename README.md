import { useState } from 'react';
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogTrigger } from '@/components/ui/dialog';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { Plus } from 'lucide-react';
import { ExpenseCategory } from '@/types/budget';

interface AddItemDialogProps {
  type: 'expense' | 'loan';
  onAddExpense?: (expense: { name: string; amount: number; category: ExpenseCategory; date: string }) => void;
  onAddLoan?: (loan: { name: string; totalAmount: number; paidAmount: number; monthlyPayment: number; dueDate: string; interestRate: number }) => void;
}

const categoryLabels: Record<ExpenseCategory, string> = {
  food: '🍔 Food Items',
  school_fees: '📚 School Fees',
  college_fees: '🎓 College Fees',
  clothes: '👕 Clothes',
  entertainment: '🎬 Entertainment',
  needs: '🛒 Daily Needs',
  other: '📦 Other',
};

export const AddItemDialog = ({ type, onAddExpense, onAddLoan }: AddItemDialogProps) => {
  const [open, setOpen] = useState(false);
  const [name, setName] = useState('');
  const [amount, setAmount] = useState('');
  const [category, setCategory] = useState<ExpenseCategory>('food');
  const [totalAmount, setTotalAmount] = useState('');
  const [monthlyPayment, setMonthlyPayment] = useState('');
  const [interestRate, setInterestRate] = useState('');
  const [dueDate, setDueDate] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    if (type === 'expense' && onAddExpense) {
      onAddExpense({
        name,
        amount: parseFloat(amount),
        category,
        date: new Date().toISOString().split('T')[0],
      });
    } else if (type === 'loan' && onAddLoan) {
      onAddLoan({
        name,
        totalAmount: parseFloat(totalAmount),
        paidAmount: 0,
        monthlyPayment: parseFloat(monthlyPayment),
        dueDate,
        interestRate: parseFloat(interestRate),
      });
    }

    // Reset form
    setName('');
    setAmount('');
    setCategory('food');
    setTotalAmount('');
    setMonthlyPayment('');
    setInterestRate('');
    setDueDate('');
    setOpen(false);
  };

  return (
    <Dialog open={open} onOpenChange={setOpen}>
      <DialogTrigger asChild>
        <Button className="gap-2 bg-primary hover:bg-primary/90 text-primary-foreground shadow-lg hover:shadow-xl transition-all duration-300">
          <Plus className="w-4 h-4" />
          Add {type === 'expense' ? 'Expense' : 'Loan'}
        </Button>
      </DialogTrigger>
      <DialogContent className="sm:max-w-[425px] bg-card border-border">
        <DialogHeader>
          <DialogTitle className="font-display text-xl">
            Add New {type === 'expense' ? 'Expense' : 'Loan'}
          </DialogTitle>
        </DialogHeader>
        <form onSubmit={handleSubmit} className="space-y-4 mt-4">
          <div className="space-y-2">
            <Label htmlFor="name">Name</Label>
            <Input
              id="name"
              value={name}
              onChange={(e) => setName(e.target.value)}
              placeholder={type === 'expense' ? 'e.g., Groceries' : 'e.g., Student Loan'}
              required
              className="bg-background border-input"
            />
          </div>

          {type === 'expense' ? (
            <>
              <div className="space-y-2">
                <Label htmlFor="amount">Amount ($)</Label>
                <Input
                  id="amount"
                  type="number"
                  value={amount}
                  onChange={(e) => setAmount(e.target.value)}
                  placeholder="0.00"
                  required
                  min="0"
                  step="0.01"
                  className="bg-background border-input"
                />
              </div>
              <div className="space-y-2">
                <Label htmlFor="category">Category</Label>
                <Select value={category} onValueChange={(v) => setCategory(v as ExpenseCategory)}>
                  <SelectTrigger className="bg-background border-input">
                    <SelectValue />
                  </SelectTrigger>
                  <SelectContent>
                    {Object.entries(categoryLabels).map(([key, label]) => (
                      <SelectItem key={key} value={key}>
                        {label}
                      </SelectItem>
                    ))}
                  </SelectContent>
                </Select>
              </div>
            </>
          ) : (
            <>
              <div className="space-y-2">
                <Label htmlFor="totalAmount">Total Amount ($)</Label>
                <Input
                  id="totalAmount"
                  type="number"
                  value={totalAmount}
                  onChange={(e) => setTotalAmount(e.target.value)}
                  placeholder="0.00"
                  required
                  min="0"
                  step="0.01"
                  className="bg-background border-input"
                />
              </div>
              <div className="grid grid-cols-2 gap-4">
                <div className="space-y-2">
                  <Label htmlFor="monthlyPayment">Monthly Payment ($)</Label>
                  <Input
                    id="monthlyPayment"
                    type="number"
                    value={monthlyPayment}
                    onChange={(e) => setMonthlyPayment(e.target.value)}
                    placeholder="0.00"
                    required
                    min="0"
                    step="0.01"
                    className="bg-background border-input"
                  />
                </div>
                <div className="space-y-2">
                  <Label htmlFor="interestRate">Interest Rate (%)</Label>
                  <Input
                    id="interestRate"
                    type="number"
                    value={interestRate}
                    onChange={(e) => setInterestRate(e.target.value)}
                    placeholder="0.0"
                    required
                    min="0"
                    step="0.1"
                    className="bg-background border-input"
                  />
                </div>
              </div>
              <div className="space-y-2">
                <Label htmlFor="dueDate">Next Due Date</Label>
                <Input
                  id="dueDate"
                  type="date"
                  value={dueDate}
                  onChange={(e) => setDueDate(e.target.value)}
                  required
                  className="bg-background border-input"
                />
              </div>
            </>
          )}

          <Button type="submit" className="w-full bg-primary hover:bg-primary/90 text-primary-foreground">
            Add {type === 'expense' ? 'Expense' : 'Loan'}
          </Button>
        </form>
      </DialogContent>
    </Dialog>
    import { BudgetSummary } from '@/types/budget';
import { Wallet, TrendingUp, TrendingDown, PiggyBank } from 'lucide-react';

interface BudgetSummaryCardProps {
  summary: BudgetSummary;
}

export const BudgetSummaryCard = ({ summary }: BudgetSummaryCardProps) => {
  const stats = [
    {
      label: 'Total Budget',
      value: summary.totalBudget,
      icon: Wallet,
      color: 'text-primary',
      bg: 'bg-primary/10',
    },
    {
      label: 'Expenses',
      value: summary.totalExpenses,
      icon: TrendingDown,
      color: 'text-destructive',
      bg: 'bg-destructive/10',
    },
    {
      label: 'Loan Payments',
      value: summary.totalLoans,
      icon: TrendingUp,
      color: 'text-warning',
      bg: 'bg-warning/10',
    },
    {
      label: 'Remaining',
      value: summary.remaining,
      icon: PiggyBank,
      color: summary.remaining >= 0 ? 'text-success' : 'text-destructive',
      bg: summary.remaining >= 0 ? 'bg-success/10' : 'bg-destructive/10',
    },
  ];

  return (
    <div className="grid grid-cols-2 lg:grid-cols-4 gap-4">
      {stats.map((stat, index) => (
        <div
          key={stat.label}
          className="p-5 rounded-2xl bg-card border border-border shadow-sm hover:shadow-lg transition-all duration-300 animate-fade-in"
          style={{ animationDelay: `${index * 50}ms` }}
        >
          <div className="flex items-center gap-3 mb-3">
            <div className={`p-2 rounded-xl ${stat.bg}`}>
              <stat.icon className={`w-5 h-5 ${stat.color}`} />
            </div>
          </div>
          <p className="text-sm text-muted-foreground mb-1">{stat.label}</p>
          <p className={`text-2xl font-bold ${stat.color}`}>
            ${Math.abs(stat.value).toLocaleString()}
            {stat.label === 'Remaining' && stat.value < 0 && (
              <span className="text-sm font-normal ml-1">(over)</span>
            )}
          </p>
        </div>
      ))}
    </div>
  );
};

 import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Separator } from '@/components/ui/separator';
import { DollarSign, Calendar, RefreshCw } from 'lucide-react';

export const CostEstimate = () => {
  const oneTimeCosts = [
    { item: 'Domain Registration', cost: 15 },
    { item: 'SSL Certificate', cost: 0, note: 'Free with hosting' },
    { item: 'Initial Design & Setup', cost: 0, note: 'DIY with Lovable' },
    { item: 'Logo & Branding', cost: 50 },
  ];

  const monthlyCosts = [
    { item: 'Web Hosting', cost: 10 },
    { item: 'Database Storage', cost: 5 },
    { item: 'Email Service', cost: 0, note: 'Free tier' },
    { item: 'Analytics', cost: 0, note: 'Free tier' },
  ];

  const oneTimeTotal = oneTimeCosts.reduce((sum, item) => sum + item.cost, 0);
  const monthlyTotal = monthlyCosts.reduce((sum, item) => sum + item.cost, 0);

  return (
    <Card className="bg-card border-border shadow-lg overflow-hidden">
      <CardHeader className="gradient-primary text-primary-foreground">
        <CardTitle className="flex items-center gap-2 font-display">
          <DollarSign className="w-5 h-5" />
          Website Cost Estimate
        </CardTitle>
      </CardHeader>
      <CardContent className="p-6 space-y-6">
        {/* One-time Costs */}
        <div>
          <div className="flex items-center gap-2 mb-4">
            <Calendar className="w-4 h-4 text-primary" />
            <h3 className="font-semibold text-foreground">One-Time Development Costs</h3>
          </div>
          <div className="space-y-3">
            {oneTimeCosts.map((item, index) => (
              <div key={index} className="flex justify-between items-center py-2 border-b border-border last:border-0">
                <div>
                  <span className="text-foreground">{item.item}</span>
                  {item.note && (
                    <span className="ml-2 text-xs text-muted-foreground">({item.note})</span>
                  )}
                </div>
                <span className="font-semibold text-foreground">
                  {item.cost === 0 ? 'Free' : `$${item.cost}`}
                </span>
              </div>
            ))}
            <div className="flex justify-between items-center pt-2 font-bold text-primary">
              <span>Total One-Time</span>
              <span>${oneTimeTotal}</span>
            </div>
          </div>
        </div>

        <Separator />

        {/* Monthly Costs */}
        <div>
          <div className="flex items-center gap-2 mb-4">
            <RefreshCw className="w-4 h-4 text-warning" />
            <h3 className="font-semibold text-foreground">Ongoing Monthly Fees</h3>
          </div>
          <div className="space-y-3">
            {monthlyCosts.map((item, index) => (
              <div key={index} className="flex justify-between items-center py-2 border-b border-border last:border-0">
                <div>
                  <span className="text-foreground">{item.item}</span>
                  {item.note && (
                    <span className="ml-2 text-xs text-muted-foreground">({item.note})</span>
                  )}
                </div>
                <span className="font-semibold text-foreground">
                  {item.cost === 0 ? 'Free' : `$${item.cost}/mo`}
                </span>
              </div>
            ))}
            <div className="flex justify-between items-center pt-2 font-bold text-warning">
              <span>Total Monthly</span>
              <span>${monthlyTotal}/mo</span>
            </div>
          </div>
        </div>

        <Separator />

        {/* First Year Total */}
        <div className="p-4 rounded-xl bg-accent">
          <div className="flex justify-between items-center">
            <span className="font-semibold text-foreground">First Year Total</span>
            <span className="text-2xl font-bold text-primary">
              ${oneTimeTotal + (monthlyTotal * 12)}
            </span>
          </div>
          <p className="text-sm text-muted-foreground mt-1">
            One-time + 12 months of recurring fees
          </p>
        </div>
      </CardContent>
    </Card>
  );
};
import { ExpenseItem, ExpenseCategory } from '@/types/budget';
import { Trash2 } from 'lucide-react';
import { Button } from '@/components/ui/button';

interface ExpenseCardProps {
  expense: ExpenseItem;
  onRemove: (id: string) => void;
}

const categoryConfig: Record<ExpenseCategory, { icon: string; color: string }> = {
  food: { icon: '🍔', color: 'bg-orange-100 border-orange-200' },
  school_fees: { icon: '📚', color: 'bg-blue-100 border-blue-200' },
  college_fees: { icon: '🎓', color: 'bg-purple-100 border-purple-200' },
  clothes: { icon: '👕', color: 'bg-pink-100 border-pink-200' },
  entertainment: { icon: '🎬', color: 'bg-red-100 border-red-200' },
  needs: { icon: '🛒', color: 'bg-green-100 border-green-200' },
  other: { icon: '📦', color: 'bg-gray-100 border-gray-200' },
};

const categoryLabels: Record<ExpenseCategory, string> = {
  food: 'Food',
  school_fees: 'School',
  college_fees: 'College',
  clothes: 'Clothes',
  entertainment: 'Entertainment',
  needs: 'Needs',
  other: 'Other',
};

export const ExpenseCard = ({ expense, onRemove }: ExpenseCardProps) => {
  const config = categoryConfig[expense.category];

  return (
    <div className={`group p-4 rounded-xl border-2 ${config.color} transition-all duration-300 hover:shadow-md animate-fade-in`}>
      <div className="flex items-center justify-between">
        <div className="flex items-center gap-3">
          <span className="text-2xl">{config.icon}</span>
          <div>
            <h4 className="font-semibold text-foreground">{expense.name}</h4>
            <p className="text-sm text-muted-foreground">{categoryLabels[expense.category]}</p>
          </div>
        </div>
        <div className="flex items-center gap-3">
          <span className="font-bold text-lg text-foreground">${expense.amount.toFixed(2)}</span>
          <Button
            variant="ghost"
            size="icon"
            onClick={() => onRemove(expense.id)}
            className="opacity-0 group-hover:opacity-100 transition-opacity text-destructive hover:text-destructive hover:bg-destructive/10"
          >
            <Trash2 className="w-4 h-4" />
          </Button>
        </div>
      </div>
    </div>
  );
};
import { LoanItem } from '@/types/budget';
import { Trash2, TrendingDown } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Progress } from '@/components/ui/progress';

interface LoanCardProps {
  loan: LoanItem;
  onRemove: (id: string) => void;
}

export const LoanCard = ({ loan, onRemove }: LoanCardProps) => {
  const progressPercent = (loan.paidAmount / loan.totalAmount) * 100;
  const remaining = loan.totalAmount - loan.paidAmount;

  return (
    <div className="group p-5 rounded-xl bg-card border border-border shadow-sm hover:shadow-lg transition-all duration-300 animate-fade-in">
      <div className="flex items-start justify-between mb-4">
        <div className="flex items-center gap-3">
          <div className="p-2 rounded-lg bg-primary/10">
            <TrendingDown className="w-5 h-5 text-primary" />
          </div>
          <div>
            <h4 className="font-semibold text-foreground">{loan.name}</h4>
            <p className="text-sm text-muted-foreground">{loan.interestRate}% APR</p>
          </div>
        </div>
        <Button
          variant="ghost"
          size="icon"
          onClick={() => onRemove(loan.id)}
          className="opacity-0 group-hover:opacity-100 transition-opacity text-destructive hover:text-destructive hover:bg-destructive/10"
        >
          <Trash2 className="w-4 h-4" />
        </Button>
      </div>

      <div className="space-y-3">
        <div className="flex justify-between text-sm">
          <span className="text-muted-foreground">Progress</span>
          <span className="font-medium text-foreground">{progressPercent.toFixed(1)}%</span>
        </div>
        <Progress value={progressPercent} className="h-2" />
        
        <div className="grid grid-cols-2 gap-4 pt-2">
          <div>
            <p className="text-xs text-muted-foreground">Remaining</p>
            <p className="font-bold text-foreground">${remaining.toLocaleString()}</p>
          </div>
          <div>
            <p className="text-xs text-muted-foreground">Monthly</p>
            <p className="font-bold text-primary">${loan.monthlyPayment}</p>
          </div>
        </div>

        <div className="pt-2 border-t border-border">
          <p className="text-xs text-muted-foreground">
            Next payment due: {new Date(loan.dueDate).toLocaleDateString()}
          </p>
        </div>
      </div>
    </div>
  );
};
import { NavLink as RouterNavLink, NavLinkProps } from "react-router-dom";
import { forwardRef } from "react";
import { cn } from "@/lib/utils";

interface NavLinkCompatProps extends Omit<NavLinkProps, "className"> {
  className?: string;
  activeClassName?: string;
  pendingClassName?: string;
}

const NavLink = forwardRef<HTMLAnchorElement, NavLinkCompatProps>(
  ({ className, activeClassName, pendingClassName, to, ...props }, ref) => {
    return (
      <RouterNavLink
        ref={ref}
        to={to}
        className={({ isActive, isPending }) =>
          cn(className, isActive && activeClassName, isPending && pendingClassName)
        }
        {...props}
      />
    );
  },
);

NavLink.displayName = "NavLink";

export { NavLink };
import { OptimizationTip } from '@/types/budget';
import { Lightbulb, ArrowRight } from 'lucide-react';

interface OptimizationTipsProps {
  tips: OptimizationTip[];
}

export const OptimizationTips = ({ tips }: OptimizationTipsProps) => {
  return (
    <section className="space-y-4">
      <div className="flex items-center gap-2">
        <Lightbulb className="w-5 h-5 text-warning" />
        <h2 className="text-xl font-display font-semibold text-foreground">
          3 Ways to Optimize Your Budget
        </h2>
      </div>

      <div className="grid gap-4 md:grid-cols-3">
        {tips.map((tip, index) => (
          <div
            key={tip.id}
            className="group relative overflow-hidden rounded-2xl bg-card border border-border p-6 hover:shadow-xl transition-all duration-500 animate-fade-in"
            style={{ animationDelay: `${index * 100}ms` }}
          >
            <div className="absolute top-0 right-0 w-32 h-32 bg-primary/5 rounded-full -translate-y-16 translate-x-16 group-hover:scale-150 transition-transform duration-500" />
            
            <div className="relative">
              <div className="flex items-center gap-3 mb-4">
                <span className="text-3xl">{tip.icon}</span>
                <span className="px-3 py-1 text-xs font-medium rounded-full bg-success/10 text-success">
                  {tip.savings}
                </span>
              </div>

              <h3 className="font-semibold text-lg text-foreground mb-2">
                {tip.title}
              </h3>
              
              <p className="text-sm text-muted-foreground leading-relaxed mb-4">
                {tip.description}
              </p>

              <div className="flex items-center gap-1 text-sm font-medium text-primary group-hover:gap-2 transition-all">
                <span>Learn more</span>
                <ArrowRight className="w-4 h-4" />
              </div>
            </div>
          </div>
        ))}
      </div>
    </section>
  );
};
import { useAuth } from '@/contexts/AuthContext';
import { useBudget } from '@/contexts/BudgetContext';
import { useNavigate } from 'react-router-dom';
import { Button } from '@/components/ui/button';
import { BudgetSummaryCard } from '@/components/BudgetSummaryCard';
import { ExpenseCard } from '@/components/ExpenseCard';
import { LoanCard } from '@/components/LoanCard';
import { AddItemDialog } from '@/components/AddItemDialog';
import { OptimizationTips } from '@/components/OptimizationTips';
import { CostEstimate } from '@/components/CostEstimate';
import { LogOut, Wallet, Receipt, CreditCard } from 'lucide-react';
import { ExpenseCategory } from '@/types/budget';

const Dashboard = () => {
  const { user, logout } = useAuth();
  const navigate = useNavigate();
  const {
    expenses,
    loans,
    addExpense,
    removeExpense,
    addLoan,
    removeLoan,
    getSummary,
    getOptimizationTips,
  } = useBudget();

  const handleLogout = () => {
    logout();
    navigate('/');
  };

  const summary = getSummary();
  const tips = getOptimizationTips();

  // Group expenses by category
  const expensesByCategory = expenses.reduce((acc, expense) => {
    if (!acc[expense.category]) {
      acc[expense.category] = [];
    }
    acc[expense.category].push(expense);
    return acc;
  }, {} as Record<ExpenseCategory, typeof expenses>);

  const categoryOrder: ExpenseCategory[] = ['food', 'school_fees', 'college_fees', 'clothes', 'entertainment', 'needs', 'other'];
  const categoryTitles: Record<ExpenseCategory, string> = {
    food: '🍔 Food Items',
    school_fees: '📚 School Fees',
    college_fees: '🎓 College Fees',
    clothes: '👕 Clothes',
    entertainment: '🎬 Entertainment',
    needs: '🛒 Daily Needs',
    other: '📦 Other Expenses',
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-background via-primary/5 to-accent/20 relative overflow-hidden">
      {/* Decorative Background Elements */}
      <div className="absolute inset-0 overflow-hidden pointer-events-none">
        <div className="absolute top-20 left-10 w-72 h-72 bg-primary/10 rounded-full blur-3xl animate-pulse" />
        <div className="absolute bottom-40 right-20 w-96 h-96 bg-warning/10 rounded-full blur-3xl animate-pulse" style={{ animationDelay: '1s' }} />
        <div className="absolute top-1/2 left-1/3 w-64 h-64 bg-accent/20 rounded-full blur-3xl animate-pulse" style={{ animationDelay: '2s' }} />
      </div>

      {/* Header */}
      <header className="sticky top-0 z-50 backdrop-blur-xl bg-background/80 border-b border-border">
        <div className="container mx-auto px-4 h-16 flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="p-2 rounded-xl gradient-primary">
              <Wallet className="w-5 h-5 text-primary-foreground" />
            </div>
            <span className="text-xl font-display font-bold text-foreground">BudgetWise</span>
          </div>
          <div className="flex items-center gap-4">
            <span className="text-sm text-muted-foreground hidden sm:block">
              Welcome, <span className="font-medium text-foreground">{user?.name}</span>
            </span>
            <Button
              variant="outline"
              size="sm"
              onClick={handleLogout}
              className="gap-2"
            >
              <LogOut className="w-4 h-4" />
              <span className="hidden sm:inline">Sign Out</span>
            </Button>
          </div>
        </div>
      </header>

      {/* Main Content */}
      <main className="container mx-auto px-4 py-8 space-y-8 relative z-10">
        {/* Budget Summary */}
        <section className="space-y-4">
          <h2 className="text-2xl font-display font-semibold text-foreground">Budget Overview</h2>
          <BudgetSummaryCard summary={summary} />
        </section>

        {/* Optimization Tips */}
        <OptimizationTips tips={tips} />

        {/* Three Column Layout */}
        <div className="grid lg:grid-cols-3 gap-8">
          {/* Column 1: Daily Expenses */}
          <div className="space-y-6">
            <div className="flex items-center justify-between">
              <div className="flex items-center gap-2">
                <Receipt className="w-5 h-5 text-primary" />
                <h2 className="text-xl font-display font-semibold text-foreground">Daily Expenses</h2>
              </div>
              <AddItemDialog type="expense" onAddExpense={addExpense} />
            </div>

            <div className="space-y-6 max-h-[600px] overflow-y-auto pr-2 scrollbar-thin">
              {categoryOrder.map((category) => {
                const categoryExpenses = expensesByCategory[category];
                if (!categoryExpenses || categoryExpenses.length === 0) return null;

                return (
                  <div key={category} className="space-y-3">
                    <h3 className="text-lg font-semibold text-foreground">
                      {categoryTitles[category]}
                    </h3>
                    <div className="grid gap-3">
                      {categoryExpenses.map((expense) => (
                        <ExpenseCard
                          key={expense.id}
                          expense={expense}
                          onRemove={removeExpense}
                        />
                      ))}
                    </div>
                  </div>
                );
              })}

              {expenses.length === 0 && (
                <div className="text-center py-12 text-muted-foreground bg-card/50 backdrop-blur-sm rounded-xl border border-border">
                  <Receipt className="w-12 h-12 mx-auto mb-4 opacity-50" />
                  <p>No expenses yet. Add your first expense!</p>
                </div>
              )}
            </div>
          </div>

          {/* Column 2: Loans Section */}
          <div className="space-y-6">
            <div className="flex items-center justify-between">
              <div className="flex items-center gap-2">
                <CreditCard className="w-5 h-5 text-warning" />
                <h2 className="text-xl font-display font-semibold text-foreground">Loans</h2>
              </div>
              <AddItemDialog type="loan" onAddLoan={addLoan} />
            </div>

            <div className="space-y-4 max-h-[600px] overflow-y-auto pr-2 scrollbar-thin">
              {loans.map((loan) => (
                <LoanCard
                  key={loan.id}
                  loan={loan}
                  onRemove={removeLoan}
                />
              ))}

              {loans.length === 0 && (
                <div className="text-center py-12 text-muted-foreground bg-card/50 backdrop-blur-sm rounded-xl border border-border">
                  <CreditCard className="w-10 h-10 mx-auto mb-3 opacity-50" />
                  <p className="text-sm">No loans tracked yet.</p>
                </div>
              )}
            </div>
          </div>

          {/* Column 3: Website Cost Estimate */}
          <div className="space-y-6">
            <CostEstimate />
          </div>
        </div>
      </main>
    </div>
  );
};

export default Dashboard;
import { useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuth } from '@/contexts/AuthContext';

const Index = () => {
  const { isAuthenticated } = useAuth();
  const navigate = useNavigate();

  useEffect(() => {
    if (isAuthenticated) {
      navigate('/dashboard');
    } else {
      navigate('/login');
    }
  }, [isAuthenticated, navigate]);

  return null;
};

export default Index;
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuth } from '@/contexts/AuthContext';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Eye, EyeOff, Wallet, TrendingUp, Shield } from 'lucide-react';
import { useToast } from '@/hooks/use-toast';

const Login = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [showPassword, setShowPassword] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const { login } = useAuth();
  const navigate = useNavigate();
  const { toast } = useToast();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setIsLoading(true);

    try {
      const success = await login(email, password);
      if (success) {
        toast({
          title: 'Welcome back!',
          description: 'Successfully logged in to your budget tracker.',
        });
        navigate('/dashboard');
      } else {
        toast({
          title: 'Login failed',
          description: 'Please check your credentials and try again.',
          variant: 'destructive',
        });
      }
    } catch (error) {
      toast({
        title: 'Error',
        description: 'Something went wrong. Please try again.',
        variant: 'destructive',
      });
    } finally {
      setIsLoading(false);
    }
  };

  const features = [
    { icon: Wallet, text: 'Track daily expenses effortlessly' },
    { icon: TrendingUp, text: 'Manage loans & payments' },
    { icon: Shield, text: 'Smart budget optimization tips' },
  ];

  return (
    <div className="min-h-screen relative overflow-hidden">
      {/* Animated gradient background */}
      <div className="absolute inset-0 gradient-bg">
        <div className="absolute inset-0 bg-[radial-gradient(ellipse_at_top_right,_var(--tw-gradient-stops))] from-primary/20 via-transparent to-transparent" />
        <div className="absolute inset-0 bg-[radial-gradient(ellipse_at_bottom_left,_var(--tw-gradient-stops))] from-blue-500/10 via-transparent to-transparent" />
      </div>

      {/* Floating shapes */}
      <div className="absolute top-20 left-10 w-72 h-72 bg-primary/10 rounded-full blur-3xl animate-float" />
      <div className="absolute bottom-20 right-10 w-96 h-96 bg-blue-500/10 rounded-full blur-3xl animate-float" style={{ animationDelay: '2s' }} />
      <div className="absolute top-1/2 left-1/3 w-64 h-64 bg-teal-500/10 rounded-full blur-3xl animate-pulse-slow" />

      {/* Grid pattern overlay */}
      <div className="absolute inset-0 bg-[url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iNDAiIGhlaWdodD0iNDAiIHZpZXdCb3g9IjAgMCA0MCA0MCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48ZyBmaWxsPSJub25lIiBmaWxsLXJ1bGU9ImV2ZW5vZGQiPjxwYXRoIGQ9Ik0wIDBoNDB2NDBIMHoiLz48cGF0aCBkPSJNNDAgMEgwdjQwaDQwVjB6TTEgMWgzOHYzOEgxVjF6IiBmaWxsPSJyZ2JhKDI1NSwyNTUsMjU1LDAuMDMpIi8+PC9nPjwvc3ZnPg==')] opacity-50" />

      {/* Content */}
      <div className="relative z-10 min-h-screen flex items-center justify-center p-4">
        <div className="w-full max-w-5xl grid md:grid-cols-2 gap-8 items-center">
          {/* Left side - Branding */}
          <div className="hidden md:block space-y-8 text-primary-foreground">
            <div className="space-y-4">
              <div className="flex items-center gap-3">
                <div className="p-3 rounded-2xl gradient-primary shadow-glow">
                  <Wallet className="w-8 h-8 text-primary-foreground" />
                </div>
                <h1 className="text-4xl font-display font-bold">BudgetWise</h1>
              </div>
              <p className="text-xl text-primary-foreground/80 max-w-md leading-relaxed">
                Take control of your finances with smart expense tracking and optimization insights.
              </p>
            </div>

            <div className="space-y-4">
              {features.map((feature, index) => (
                <div
                  key={index}
                  className="flex items-center gap-4 p-4 rounded-xl bg-white/5 backdrop-blur-sm border border-white/10 animate-slide-in-right"
                  style={{ animationDelay: `${index * 150}ms` }}
                >
                  <div className="p-2 rounded-lg bg-white/10">
                    <feature.icon className="w-5 h-5 text-primary-foreground" />
                  </div>
                  <span className="text-primary-foreground/90">{feature.text}</span>
                </div>
              ))}
            </div>
          </div>

          {/* Right side - Login form */}
          <Card className="w-full max-w-md mx-auto glass-card border-white/20 shadow-2xl animate-scale-in">
            <CardHeader className="space-y-1 text-center pb-2">
              <div className="md:hidden flex justify-center mb-4">
                <div className="p-3 rounded-2xl gradient-primary shadow-lg">
                  <Wallet className="w-8 h-8 text-primary-foreground" />
                </div>
              </div>
              <CardTitle className="text-2xl font-display text-foreground">Welcome Back</CardTitle>
              <CardDescription className="text-muted-foreground">
                Sign in to access your budget dashboard
              </CardDescription>
            </CardHeader>
            <CardContent className="pt-4">
              <form onSubmit={handleSubmit} className="space-y-4">
                <div className="space-y-2">
                  <Label htmlFor="email" className="text-foreground">Email</Label>
                  <Input
                    id="email"
                    type="email"
                    placeholder="you@example.com"
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                    required
                    className="h-12 bg-background/50 border-border focus:border-primary transition-colors"
                  />
                </div>
                <div className="space-y-2">
                  <Label htmlFor="password" className="text-foreground">Password</Label>
                  <div className="relative">
                    <Input
                      id="password"
                      type={showPassword ? 'text' : 'password'}
                      placeholder="••••••••"
                      value={password}
                      onChange={(e) => setPassword(e.target.value)}
                      required
                      minLength={6}
                      className="h-12 bg-background/50 border-border focus:border-primary transition-colors pr-12"
                    />
                    <button
                      type="button"
                      onClick={() => setShowPassword(!showPassword)}
                      className="absolute right-4 top-1/2 -translate-y-1/2 text-muted-foreground hover:text-foreground transition-colors"
                    >
                      {showPassword ? <EyeOff className="w-5 h-5" /> : <Eye className="w-5 h-5" />}
                    </button>
                  </div>
                </div>

                <Button
                  type="submit"
                  disabled={isLoading}
                  className="w-full h-12 gradient-primary text-primary-foreground font-semibold shadow-lg hover:shadow-xl hover:scale-[1.02] transition-all duration-300"
                >
                  {isLoading ? (
                    <div className="flex items-center gap-2">
                      <div className="w-5 h-5 border-2 border-primary-foreground/30 border-t-primary-foreground rounded-full animate-spin" />
                      Signing in...
                    </div>
                  ) : (
                    'Sign In'
                  )}
                </Button>

                <p className="text-center text-sm text-muted-foreground">
                  Demo: Enter any email and password (6+ chars)
                </p>
              </form>
            </CardContent>
          </Card>
        </div>
      </div>
      import { useLocation } from "react-router-dom";
import { useEffect } from "react";

const NotFound = () => {
  const location = useLocation();

  useEffect(() => {
    console.error("404 Error: User attempted to access non-existent route:", location.pathname);
  }, [location.pathname]);

  return (
    <div className="flex min-h-screen items-center justify-center bg-muted">
      <div className="text-center">
        <h1 className="mb-4 text-4xl font-bold">404</h1>
        <p className="mb-4 text-xl text-muted-foreground">Oops! Page not found</p>
        <a href="/" className="text-primary underline hover:text-primary/90">
          Return to Home
        </a>
      </div>
    </div>
  );
};

export default NotFound;
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
{
  "name": "vite_react_shadcn_ts",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build:dev": "vite build --mode development",
    "lint": "eslint .",
    "preview": "vite preview"
  },
  "dependencies": {
    "@hookform/resolvers": "^3.10.0",
    "@radix-ui/react-accordion": "^1.2.11",
    "@radix-ui/react-alert-dialog": "^1.1.14",
    "@radix-ui/react-aspect-ratio": "^1.1.7",
    "@radix-ui/react-avatar": "^1.1.10",
    "@radix-ui/react-checkbox": "^1.3.2",
    "@radix-ui/react-collapsible": "^1.1.11",
    "@radix-ui/react-context-menu": "^2.2.15",
    "@radix-ui/react-dialog": "^1.1.14",
    "@radix-ui/react-dropdown-menu": "^2.1.15",
    "@radix-ui/react-hover-card": "^1.1.14",
    "@radix-ui/react-label": "^2.1.7",
    "@radix-ui/react-menubar": "^1.1.15",
    "@radix-ui/react-navigation-menu": "^1.2.13",
    "@radix-ui/react-popover": "^1.1.14",
    "@radix-ui/react-progress": "^1.1.7",
    "@radix-ui/react-radio-group": "^1.3.7",
    "@radix-ui/react-scroll-area": "^1.2.9",
    "@radix-ui/react-select": "^2.2.5",
    "@radix-ui/react-separator": "^1.1.7",
    "@radix-ui/react-slider": "^1.3.5",
    "@radix-ui/react-slot": "^1.2.3",
    "@radix-ui/react-switch": "^1.2.5",
    "@radix-ui/react-tabs": "^1.1.12",
    "@radix-ui/react-toast": "^1.2.14",
    "@radix-ui/react-toggle": "^1.1.9",
    "@radix-ui/react-toggle-group": "^1.1.10",
    "@radix-ui/react-tooltip": "^1.2.7",
    "@tanstack/react-query": "^5.83.0",
    "class-variance-authority": "^0.7.1",
    "clsx": "^2.1.1",
    "cmdk": "^1.1.1",
    "date-fns": "^3.6.0",
    "embla-carousel-react": "^8.6.0",
    "input-otp": "^1.4.2",
    "lucide-react": "^0.462.0",
    "next-themes": "^0.3.0",
    "react": "^18.3.1",
    "react-day-picker": "^8.10.1",
    "react-dom": "^18.3.1",
    "react-hook-form": "^7.61.1",
    "react-resizable-panels": "^2.1.9",
    "react-router-dom": "^6.30.1",
    "recharts": "^2.15.4",
    "sonner": "^1.7.4",
    "tailwind-merge": "^2.6.0",
    "tailwindcss-animate": "^1.0.7",
    "vaul": "^0.9.9",
    "zod": "^3.25.76"
  },
  "devDependencies": {
    "@eslint/js": "^9.32.0",
    "@tailwindcss/typography": "^0.5.16",
    "@types/node": "^22.16.5",
    "@types/react": "^18.3.23",
    "@types/react-dom": "^18.3.7",
    "@vitejs/plugin-react-swc": "^3.11.0",
    "autoprefixer": "^10.4.21",
    "eslint": "^9.32.0",
    "eslint-plugin-react-hooks": "^5.2.0",
    "eslint-plugin-react-refresh": "^0.4.20",
    "globals": "^15.15.0",
    "lovable-tagger": "^1.1.13",
    "postcss": "^8.5.6",
    "tailwindcss": "^3.4.17",
    "typescript": "^5.8.3",
    "typescript-eslint": "^8.38.0",
    "vite": "^5.4.19"
  }
}
import type { Config } from "tailwindcss";

export default {
  darkMode: ["class"],
  content: ["./pages/**/*.{ts,tsx}", "./components/**/*.{ts,tsx}", "./app/**/*.{ts,tsx}", "./src/**/*.{ts,tsx}"],
  prefix: "",
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: {
        "2xl": "1400px",
      },
    },
    extend: {
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        display: ['Playfair Display', 'serif'],
      },
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        success: {
          DEFAULT: "hsl(var(--success))",
          foreground: "hsl(var(--success-foreground))",
        },
        warning: {
          DEFAULT: "hsl(var(--warning))",
          foreground: "hsl(var(--warning-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
        sidebar: {
          DEFAULT: "hsl(var(--sidebar-background))",
          foreground: "hsl(var(--sidebar-foreground))",
          primary: "hsl(var(--sidebar-primary))",
          "primary-foreground": "hsl(var(--sidebar-primary-foreground))",
          accent: "hsl(var(--sidebar-accent))",
          "accent-foreground": "hsl(var(--sidebar-accent-foreground))",
          border: "hsl(var(--sidebar-border))",
          ring: "hsl(var(--sidebar-ring))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      boxShadow: {
        'sm': 'var(--shadow-sm)',
        'md': 'var(--shadow-md)',
        'lg': 'var(--shadow-lg)',
        'xl': 'var(--shadow-xl)',
        'glow': 'var(--shadow-glow)',
      },
      keyframes: {
        "accordion-down": {
          from: { height: "0" },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: "0" },
        },
        "fade-in": {
          from: { opacity: "0", transform: "translateY(10px)" },
          to: { opacity: "1", transform: "translateY(0)" },
        },
        "slide-in-right": {
          from: { opacity: "0", transform: "translateX(20px)" },
          to: { opacity: "1", transform: "translateX(0)" },
        },
        "scale-in": {
          from: { opacity: "0", transform: "scale(0.95)" },
          to: { opacity: "1", transform: "scale(1)" },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
        "fade-in": "fade-in 0.5s ease-out forwards",
        "slide-in-right": "slide-in-right 0.4s ease-out forwards",
        "scale-in": "scale-in 0.3s ease-out forwards",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
} satisfies Config;
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.ts",
    "css": "src/index.css",
    "baseColor": "slate",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui",
    "lib": "@/lib",
    "hooks": "@/hooks"
  }
}
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react-swc";
import path from "path";
import { componentTagger } from "lovable-tagger";

// https://vitejs.dev/config/
export default defineConfig(({ mode }) => ({
  server: {
    host: "::",
    port: 8080,
  },
  plugins: [react(), mode === "development" && componentTagger()].filter(Boolean),
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
}));
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <!-- TODO: Set the document title to the name of your application -->
    <title>Lovable App</title>
    <meta name="description" content="Lovable Generated Project" />
    <meta name="author" content="Lovable" />

    <!-- TODO: Update og:title to match your application name -->
    <meta property="og:title" content="Lovable App" />
    <meta property="og:description" content="Lovable Generated Project" />
    <meta property="og:type" content="website" />
    <meta property="og:image" content="https://lovable.dev/opengraph-image-p98pqg.png" />

    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:site" content="@Lovable" />
    <meta name="twitter:image" content="https://lovable.dev/opengraph-image-p98pqg.png" />
  </head>

  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>

 );
};
