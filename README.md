import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import warnings

warnings.filterwarnings("ignore")
sns.set_theme(style="whitegrid", palette="muted")
plt.rcParams["figure.dpi"] = 130
os.makedirs("/kaggle/working/images", exist_ok=True)

# ── CLEAN & TRANSFORM ─────────────────────────────────────────
def clean_price(col):
    return col.astype(str).str.replace("[₹,]", "", regex=True).str.strip()

df["discounted_price"]    = pd.to_numeric(clean_price(df["discounted_price"]), errors="coerce")
df["actual_price"]        = pd.to_numeric(clean_price(df["actual_price"]),     errors="coerce")
df["discount_percentage"] = pd.to_numeric(
    df["discount_percentage"].astype(str).str.replace("%", "").str.strip(), errors="coerce")
df["rating"]       = pd.to_numeric(df["rating"].astype(str).str.strip(), errors="coerce")
df["rating_count"] = pd.to_numeric(
    df["rating_count"].astype(str).str.replace(",", "").str.strip(), errors="coerce")

df.dropna(subset=["discounted_price", "actual_price", "rating"], inplace=True)
df["savings"]       = df["actual_price"] - df["discounted_price"]
df["main_category"] = df["category"].astype(str).str.split("|").str[0].str.strip()

print("Clean shape:", df.shape)

# ── KEY KPIs ──────────────────────────────────────────────────
print("\n========== KEY KPIs ==========")
kpis = {
    "Total Products":       len(df),
    "Avg Discounted Price": f"₹{df['discounted_price'].mean():,.0f}",
    "Avg Actual Price":     f"₹{df['actual_price'].mean():,.0f}",
    "Avg Discount %":       f"{df['discount_percentage'].mean():.1f}%",
    "Avg Rating":           f"{df['rating'].mean():.2f} / 5",
    "Avg Savings":          f"₹{df['savings'].mean():,.0f}",
}
for k, v in kpis.items():
    print(f"  {k:<28} {v}")

# ── CHART 1: EDA DISTRIBUTIONS ────────────────────────────────
fig, axes = plt.subplots(2, 2, figsize=(14, 9))

sns.histplot(df["rating"], bins=20, kde=True, ax=axes[0,0], color="#4A90D9")
axes[0,0].set_title("Rating Distribution")

sns.histplot(df["discounted_price"], bins=40, kde=True, ax=axes[0,1], color="#E06C4D")
axes[0,1].set_title("Discounted Price Distribution")
axes[0,1].set_yscale("log")

sns.histplot(df["discount_percentage"], bins=30, kde=True, ax=axes[1,0], color="#5CB85C")
axes[1,0].set_title("Discount % Distribution")

sns.histplot(df["savings"], bins=40, kde=True, ax=axes[1,1], color="#9B59B6")
axes[1,1].set_title("Savings Distribution")
axes[1,1].set_yscale("log")

plt.suptitle("Amazon Sales — Univariate EDA", fontsize=15, fontweight="bold")
plt.tight_layout()
plt.savefig("/kaggle/working/images/01_eda_distributions.png", bbox_inches="tight")
plt.show()

# ── CHART 2: TOP CATEGORIES ───────────────────────────────────
cat_counts = df["main_category"].value_counts().head(12)

plt.figure(figsize=(12, 5))
sns.barplot(x=cat_counts.values, y=cat_counts.index, palette="Blues_d")
plt.title("Top 12 Product Categories by Count", fontsize=14, fontweight="bold")
plt.xlabel("Number of Products")
plt.tight_layout()
plt.savefig("/kaggle/working/images/02_category_counts.png", bbox_inches="tight")
plt.show()

# ── CHART 3: CATEGORY PERFORMANCE ────────────────────────────
cat_stats = df.groupby("main_category").agg(
    product_count=("product_id",         "count"),
    avg_price    =("discounted_price",    "mean"),
    avg_rating   =("rating",              "mean"),
    avg_discount =("discount_percentage", "mean"),
).sort_values("product_count", ascending=False).head(10).reset_index()

fig, axes = plt.subplots(1, 3, figsize=(18, 6))

sns.barplot(data=cat_stats, y="main_category", x="avg_price",    palette="YlOrRd",   ax=axes[0])
axes[0].set_title("Avg Price by Category");     axes[0].set_ylabel("")

sns.barplot(data=cat_stats, y="main_category", x="avg_rating",   palette="Blues_d",  ax=axes[1])
axes[1].set_title("Avg Rating by Category");    axes[1].set_ylabel(""); axes[1].set_xlim(3, 5)

sns.barplot(data=cat_stats, y="main_category", x="avg_discount", palette="Greens_d", ax=axes[2])
axes[2].set_title("Avg Discount % by Category"); axes[2].set_ylabel("")

plt.suptitle("Category Performance Analysis", fontsize=15, fontweight="bold")
plt.tight_layout()
plt.savefig("/kaggle/working/images/03_category_analysis.png", bbox_inches="tight")
plt.show()

# ── CHART 4: PRICE vs RATING ──────────────────────────────────
top_cats = df["main_category"].value_counts().head(6).index

fig, ax = plt.subplots(figsize=(12, 7))
for cat in top_cats:
    subset = df[df["main_category"] == cat]
    ax.scatter(subset["discounted_price"], subset["rating"], label=cat, alpha=0.5, s=30)

ax.set_xscale("log")
ax.set_xlabel("Discounted Price (₹, log scale)")
ax.set_ylabel("Rating")
ax.set_title("Price vs Rating by Category", fontsize=14, fontweight="bold")
ax.legend(loc="lower right", fontsize=9)
ax.axhline(df["rating"].mean(), color="red", linestyle="--", linewidth=1)
plt.tight_layout()
plt.savefig("/kaggle/working/images/04_price_vs_rating.png", bbox_inches="tight")
plt.show()

# ── CHART 5: DISCOUNT BUCKET ──────────────────────────────────
bins   = [0, 20, 40, 60, 80, 100]
labels = ["0-20%", "21-40%", "41-60%", "61-80%", "81-100%"]
df["discount_bucket"] = pd.cut(df["discount_percentage"], bins=bins, labels=labels, right=True)

bucket_stats = df.groupby("discount_bucket", observed=True).agg(
    count     =("product_id", "count"),
    avg_rating=("rating",     "mean"),
).reset_index()

fig, ax1 = plt.subplots(figsize=(10, 5))
ax2 = ax1.twinx()
ax1.bar(bucket_stats["discount_bucket"], bucket_stats["count"], color="#4A90D9", alpha=0.7, label="Product count")
ax2.plot(bucket_stats["discount_bucket"], bucket_stats["avg_rating"], color="#E06C4D", marker="o", linewidth=2.5, label="Avg Rating")
ax1.set_xlabel("Discount Bucket")
ax1.set_ylabel("Number of Products", color="#4A90D9")
ax2.set_ylabel("Average Rating",     color="#E06C4D")
ax2.set_ylim(3.5, 5.0)
ax1.set_title("Discount Level vs Product Count & Rating", fontsize=14, fontweight="bold")
lines1, labels1 = ax1.get_legend_handles_labels()
lines2, labels2 = ax2.get_legend_handles_labels()
ax1.legend(lines1 + lines2, labels1 + labels2, loc="upper left")
plt.tight_layout()
plt.savefig("/kaggle/working/images/05_discount_bucket.png", bbox_inches="tight")
plt.show()

# ── CHART 6: CORRELATION HEATMAP ─────────────────────────────
cols = ["discounted_price","actual_price","discount_percentage","rating","rating_count","savings"]
corr = df[cols].corr()

plt.figure(figsize=(9, 7))
mask = np.triu(np.ones_like(corr, dtype=bool))
sns.heatmap(corr, annot=True, fmt=".2f", cmap="coolwarm",
            mask=mask, vmin=-1, vmax=1, linewidths=0.5, square=True)
plt.title("Correlation Matrix — Key Variables", fontsize=14, fontweight="bold")
plt.tight_layout()
plt.savefig("/kaggle/working/images/06_correlation.png", bbox_inches="tight")
plt.show()

# ── TOP 10 MOST REVIEWED ──────────────────────────────────────
print("\n========== TOP 10 MOST REVIEWED PRODUCTS ==========")
top_reviewed = (
    df.nlargest(10, "rating_count")[["product_name","rating","rating_count","discounted_price"]]
    .reset_index(drop=True)
)
top_reviewed["product_name"] = top_reviewed["product_name"].str[:55]
print(top_reviewed.to_string(index=False))

# ── FINAL INSIGHTS ────────────────────────────────────────────
print("\n========== KEY INSIGHTS ==========")
best_cat   = cat_stats.loc[cat_stats["avg_rating"].idxmax(),  "main_category"]
cheapest   = cat_stats.loc[cat_stats["avg_price"].idxmin(),   "main_category"]
top_bucket = bucket_stats.loc[bucket_stats["avg_rating"].idxmax(), "discount_bucket"]

print(f"  Highest rated category:         {best_cat}")
print(f"  Most affordable category:       {cheapest}")
print(f"  Best discount bucket by rating: {top_bucket}")
print(f"  Price-Rating correlation:       {corr.loc['discounted_price','rating']:.2f}")
print("\nAll 6 charts saved to /kaggle/working/images/")
print("Project complete!")
