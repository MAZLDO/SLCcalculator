import React, { useState, useMemo } from "react";

// Student Loan Repayment Calculator styled like GOV.UK

export default function StudentLoanCalculator() {
  const loanPresets = {
    "Plan 2 (England/Wales)": {
      threshold: 28470,
      interestRate: 7.3,
      writeOffYears: 30,
    },
    "Plan 1": { threshold: 22015, interestRate: 1.5, writeOffYears: 25 },
    "Plan 5": { threshold: 25000, interestRate: 6.5, writeOffYears: 40 },
    "Custom": { threshold: 28000, interestRate: 5.0, writeOffYears: 30 },
  };

  const maintenancePresets = {
    "No maintenance": 0,
    "Living at home": 0,
    "Living away (outside London)": 9000,
    "Living in London": 12000,
  };

  const [loanType, setLoanType] = useState("Plan 2 (England/Wales)");
  const [threshold, setThreshold] = useState(loanPresets[loanType].threshold);
  const [interestRate, setInterestRate] = useState(loanPresets[loanType].interestRate);
  const [writeOffYears, setWriteOffYears] = useState(loanPresets[loanType].writeOffYears);

  const [tuitionYears, setTuitionYears] = useState(3);
  const [tuitionPerYear, setTuitionPerYear] = useState(9250);

  const [includeMaintenance, setIncludeMaintenance] = useState(true);
  const [maintenanceChoice, setMaintenanceChoice] = useState("Living away (outside London)");
  const [maintenanceCustom, setMaintenanceCustom] = useState(maintenancePresets[maintenanceChoice]);

  const [salary, setSalary] = useState(60000);
  const [salaryGrowthPct, setSalaryGrowthPct] = useState(1.5);
  const [extraMonthly, setExtraMonthly] = useState(0);

  React.useEffect(() => {
    const preset = loanPresets[loanType];
    if (preset) {
      setThreshold(preset.threshold);
      setInterestRate(preset.interestRate);
      setWriteOffYears(preset.writeOffYears);
    }
  }, [loanType]);

  React.useEffect(() => {
    const val = maintenancePresets[maintenanceChoice] ?? maintenanceCustom;
    setMaintenanceCustom(val);
  }, [maintenanceChoice]);

  const principal = useMemo(() => {
    const tuition = tuitionYears * tuitionPerYear;
    const maintenance = includeMaintenance ? maintenanceCustom * tuitionYears : 0;
    return Math.max(0, Math.round(tuition + maintenance));
  }, [tuitionYears, tuitionPerYear, includeMaintenance, maintenanceCustom]);

  function simulateRepayment({ principal, salaryStart, threshold, interestRate, salaryGrowthPct, extraMonthly, writeOffYears }) {
    const timeline = [];
    let balance = principal;
    let salary = salaryStart;
    const annualExtra = extraMonthly * 12;
    const maxYears = Math.max(1, Math.round(writeOffYears));

    for (let year = 1; year <= maxYears; year++) {
      const excess = Math.max(0, salary - threshold);
      const requiredRepayment = 0.09 * excess;
      const payment = requiredRepayment + annualExtra;
      const interest = (interestRate / 100) * balance;
      const newBalance = Math.max(0, balance + interest - payment);

      timeline.push({ year, salary: Math.round(salary), balance: Math.round(newBalance), paid: Math.round(payment), interest: Math.round(interest) });

      if (newBalance <= 0) return { yearsToRepay: year, timeline };
      balance = newBalance;
      salary = salary * (1 + salaryGrowthPct / 100);
    }
    return { yearsToRepay: null, timeline };
  }

  const result = useMemo(() => simulateRepayment({ principal, salaryStart: salary, threshold, interestRate, salaryGrowthPct, extraMonthly, writeOffYears }), [principal, salary, threshold, interestRate, salaryGrowthPct, extraMonthly, writeOffYears]);

  return (
    <div className="font-[GDS-Transport,Arial,sans-serif] bg-[#f3f2f1] min-h-screen">
      {/* GOV.UK header */}
      <header className="bg-black text-white p-4 flex items-center">
        <img src="https://assets.publishing.service.gov.uk/government/assets/organisation/crest/crest-48px--white-2009fef13f48cfb5f992c2f3f4ecf4952a55df6d63d9e46e8f1f4955e939a4a3.png" alt="GOV.UK" className="h-8 mr-3" />
        <h1 className="text-xl font-bold tracking-tight">GOV.UK Student Loan Calculator</h1>
      </header>

      <main className="max-w-6xl mx-auto p-6 space-y-6">
        <section className="bg-white p-6 shadow-md border-t-8 border-[#1d70b8]">
          <h2 className="text-lg font-bold mb-4">Loan & Income Inputs</h2>

          <div className="grid gap-4">
            <label className="block text-sm font-semibold">
              Loan type
              <select className="mt-1 block w-full border border-black p-2" value={loanType} onChange={(e) => setLoanType(e.target.value)}>
                {Object.keys(loanPresets).map((k) => (
                  <option key={k} value={k}>{k}</option>
                ))}
              </select>
            </label>

            <label className="block text-sm font-semibold">
              Repayment threshold (annual)
              <input className="mt-1 block w-full border border-black p-2" type="number" value={threshold} onChange={(e) => setThreshold(Number(e.target.value))} />
            </label>

            <div className="grid grid-cols-2 gap-3">
              <label className="block text-sm font-semibold">
                Interest rate (%)
                <input className="mt-1 block w-full border border-black p-2" type="number" step="0.1" value={interestRate} onChange={(e) => setInterestRate(Number(e.target.value))} />
              </label>
              <label className="block text-sm font-semibold">
                Write-off after (years)
                <input className="mt-1 block w-full border border-black p-2" type="number" value={writeOffYears} onChange={(e) => setWriteOffYears(Number(e.target.value))} />
              </label>
            </div>

            <div className="grid grid-cols-2 gap-3">
              <label className="block text-sm font-semibold">
                Tuition years
                <input className="mt-1 block w-full border border-black p-2" type="number" value={tuitionYears} onChange={(e) => setTuitionYears(Number(e.target.value))} />
              </label>
              <label className="block text-sm font-semibold">
                Tuition per year
                <input className="mt-1 block w-full border border-black p-2" type="number" value={tuitionPerYear} onChange={(e) => setTuitionPerYear(Number(e.target.value))} />
              </label>
            </div>

            <label className="flex items-center space-x-2">
              <input type="checkbox" checked={includeMaintenance} onChange={(e) => setIncludeMaintenance(e.target.checked)} />
              <span className="text-sm font-semibold">Include maintenance loan</span>
            </label>

            {includeMaintenance && (
              <div className="grid gap-3">
                <label className="block text-sm font-semibold">
                  Maintenance preset
                  <select className="mt-1 block w-full border border-black p-2" value={maintenanceChoice} onChange={(e) => setMaintenanceChoice(e.target.value)}>
                    {Object.keys(maintenancePresets).map((k) => (
                      <option key={k} value={k}>{k}</option>
                    ))}
                    <option value="Custom">Custom</option>
                  </select>
                </label>
                <label className="block text-sm font-semibold">
                  Maintenance per year (editable)
                  <input className="mt-1 block w-full border border-black p-2" type="number" value={maintenanceCustom} onChange={(e) => setMaintenanceCustom(Number(e.target.value))} />
                </label>
              </div>
            )}

            <label className="block text-sm font-semibold">
              Annual salary (gross)
              <input className="mt-1 block w-full border border-black p-2" type="number" value={salary} onChange={(e) => setSalary(Number(e.target.value))} />
            </label>

            <div className="grid grid-cols-2 gap-3">
              <label className="block text-sm font-semibold">
                Salary growth (% per year)
                <input className="mt-1 block w-full border border-black p-2" type="number" step="0.1" value={salaryGrowthPct} onChange={(e) => setSalaryGrowthPct(Number(e.target.value))} />
              </label>
              <label className="block text-sm font-semibold">
                Extra monthly payments (£)
                <input className="mt-1 block w-full border border-black p-2" type="number" value={extraMonthly} onChange={(e) => setExtraMonthly(Number(e.target.value))} />
              </label>
            </div>
          </div>
        </section>

        <section className="bg-white p-6 shadow-md border-t-8 border-[#00703c]">
          <h2 className="text-lg font-bold mb-4">Results</h2>

          <div className="grid gap-4">
            <div className="p-3 border border-black">
              <div className="text-sm">Initial loan balance</div>
              <div className="text-2xl font-bold">£{principal.toLocaleString()}</div>
            </div>

            <div className="p-3 border border-black">
              <div className="text-sm">Estimated annual repayments</div>
              <div className="text-xl font-semibold">£{(Math.round(0.09 * Math.max(0, salary - threshold) * 100) / 100).toLocaleString()} / year</div>
              <div className="text-sm">≈ £{(Math.round(0.09 * Math.max(0, salary - threshold) / 12 * 100) / 100).toLocaleString()} / month</div>
            </div>

            <div className="p-3 border border-black">
              {result.yearsToRepay ? (
                <>
                  <div className="text-sm">Estimated years to repay</div>
                  <div className="text-2xl font-bold">{result.yearsToRepay} years</div>
                </>
              ) : (
                <>
                  <div className="text-sm">Estimated outcome</div>
                  <div className="text-xl font-semibold">Not repaid before write-off ({writeOffYears} years)</div>
                  <div className="text-sm mt-2">Remaining balance after {writeOffYears} years: <strong>£{result.timeline[result.timeline.length - 1].balance.toLocaleString()}</strong></div>
                </>
              )}
            </div>
          </div>

          <h3 className="mt-6 text-sm font-bold">Year-by-year table</h3>
          <div className="overflow-x-auto">
            <table className="w-full border border-black mt-2">
              <thead className="bg-[#f3f2f1]">
                <tr>
                  <th className="p-2 border border-black">Year</th>
                  <th className="p-2 border border-black">Salary</th>
                  <th className="p-2 border border-black">Payment</th>
                  <th className="p-2 border border-black">Interest</th>
                  <th className="p-2 border border-black">End balance</th>
                </tr>
              </thead>
              <tbody>
                {result.timeline.map((r) => (
                  <tr key={r.year}>
                    <td className="p-2 border border-black">{r.year}</td>
                    <td className="p-2 border border-black">£{r.salary.toLocaleString()}</td>
                    <td className="p-2 border border-black">£{r.paid.toLocaleString()}</td>
                    <td className="p-2 border border-black">£{r.interest.toLocaleString()}</td>
                    <td className="p-2 border border-black">£{r.balance.toLocaleString()}</td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </section>
      </main>

      <footer className="max-w-6xl mx-auto p-6 text-sm">
        <p className="text-[#505a5f]">This is a simplified model. Actual UK student loan calculations are monthly and depend on RPI-based interest rules. Adjust the inputs to match your situation.</p>
      </footer>
    </div>
  );
}
