# Demographic-Data-Analyzer

import pandas as pd

def calculate_demographic_data(save_csv=True, print_data=True):
    # Load the dataset
    df = pd.read_csv('adult.data.csv')

    # 1. How many people of each race are represented in this dataset?
    race_count = df['race'].value_counts()

    # 2. What is the average age of men?
    average_age_men = round(df[df['sex'] == 'Male']['age'].mean(), 1)

    # 3. What is the percentage of people who have a Bachelor's degree?
    percentage_bachelors = round((df['education'] == 'Bachelors').mean() * 100, 1)

    # Define advanced education
    advanced_education = df['education'].isin(['Bachelors', 'Masters', 'Doctorate'])

    # 4. What percentage of people with advanced education make more than 50K?
    higher_education_rich = round(
        (df[advanced_education]['salary'] == '>50K').mean() * 100, 1
    )

    # 5. What percentage of people without advanced education make more than 50K?
    lower_education = ~advanced_education
    lower_education_rich = round(
        (df[lower_education]['salary'] == '>50K').mean() * 100, 1
    )

    # 6. What is the minimum number of hours a person works per week?
    min_work_hours = df['hours-per-week'].min()

    # 7. What percentage of the people who work the minimum number of hours per week have a salary of more than 50K?
    min_workers = df[df['hours-per-week'] == min_work_hours]
    rich_min_workers = min_workers[min_workers['salary'] == '>50K']
    rich_percentage = round((len(rich_min_workers) / len(min_workers)) * 100, 1)

    # 8. What country has the highest percentage of people that earn >50K?
    country_counts = df['native-country'].value_counts()
    rich_country_counts = df[df['salary'] == '>50K']['native-country'].value_counts()
    rich_percentage_by_country = (rich_country_counts / country_counts) * 100
    highest_earning_country = rich_percentage_by_country.idxmax()
    highest_earning_country_percentage = round(rich_percentage_by_country.max(), 1)

    # 9. Identify the most popular occupation for those who earn >50K in India.
    india_rich = df[(df['native-country'] == 'India') & (df['salary'] == '>50K')]
    top_occupation_india = india_rich['occupation'].value_counts().idxmax()

    if print_data:
        print("Number of each race:\n", race_count)
        print("Average age of men:", average_age_men)
        print("Percentage with Bachelor's degrees:", percentage_bachelors)
        print("Percentage with higher education earning >50K:", higher_education_rich)
        print("Percentage without higher education earning >50K:", lower_education_rich)
        print("Minimum work hours:", min_work_hours)
        print("Percentage of rich among those who work minimum hours:", rich_percentage)
        print("Country with highest percentage of rich:", highest_earning_country)
        print("Highest percentage of rich people in country:", highest_earning_country_percentage)
        print("Top occupations in India for those earning >50K:", top_occupation_india)

    # Prepare a summary dictionary with string values and numbers
    summary_data = {
        'average_age_men': [average_age_men],
        'percentage_bachelors': [percentage_bachelors],
        'higher_education_rich': [higher_education_rich],
        'lower_education_rich': [lower_education_rich],
        'min_work_hours': [min_work_hours],
        'rich_percentage_min_workers': [rich_percentage],
        'highest_earning_country': [highest_earning_country],
        'highest_earning_country_percentage': [highest_earning_country_percentage],
        'top_occupation_india': [top_occupation_india],
    }

    # Convert race_count Series to DataFrame for CSV
    race_df = race_count.reset_index()
    race_df.columns = ['race', 'count']

    if save_csv:
        # Save race counts separately
        race_df.to_csv('race_count.csv', index=False)
        
        # Save summary data as a CSV with one row
        summary_df = pd.DataFrame(summary_data)
        summary_df.to_csv('demographic_summary.csv', index=False)

    return {
        'race_count': race_count,
        'average_age_men': average_age_men,
        'percentage_bachelors': percentage_bachelors,
        'higher_education_rich': higher_education_rich,
        'lower_education_rich': lower_education_rich,
        'min_work_hours': min_work_hours,
        'rich_percentage': rich_percentage,
        'highest_earning_country': highest_earning_country,
        'highest_earning_country_percentage': highest_earning_country_percentage,
        'top_occupation_india': top_occupation_india
    }


if __name__ == '__main__':
    calculate_demographic_data()
