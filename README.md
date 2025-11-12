#!/usr/bin/env python3

import json
from collections import Counter
from datetime import datetime

def analyze_github_data():
    # Load data
    with open('repositories_000001.json', 'r') as f:
        repos = json.load(f)

    with open('users_000001.json', 'r') as f:
        users = json.load(f)

    print('=== GITHUB DATA ANALYSIS ===')
    print(f'Total Repositories: {len(repos)}')
    print(f'Total Users: {len(users)}')
    print()

    # Repository analysis
    private_repos = sum(1 for repo in repos if repo.get('private', False))
    public_repos = len(repos) - private_repos

    print('=== REPOSITORY BREAKDOWN ===')
    print(f'Private Repositories: {private_repos}')
    print(f'Public Repositories: {public_repos}')
    print()

    # Language analysis
    languages = []
    for repo in repos:
        if 'language' in repo and repo['language']:
            languages.append(repo['language'])

    if languages:
        lang_counts = Counter(languages)
        print('=== TOP PROGRAMMING LANGUAGES ===')
        for lang, count in lang_counts.most_common(10):
            print(f'{lang}: {count} repositories')
        print()

    # User analysis
    print('=== USER INFORMATION ===')
    for user in users:
        if user.get('login') == 'yahya-mouiguina':
            print(f"Main User: {user.get('name', 'N/A')} (@{user.get('login')})")
            print(f"Bio: {user.get('bio', 'No bio')}")
            print(f"Created: {user.get('created_at', 'Unknown')}")
            if 'emails' in user:
                print(f"Emails: {len(user['emails'])} registered")
                for email in user['emails']:
                    print(f"  - {email['address']} (Primary: {email.get('primary', False)})")
            break

    # Repository creation timeline
    repo_years = []
    for repo in repos:
        if 'created_at' in repo:
            try:
                year = datetime.fromisoformat(repo['created_at'].replace('Z', '+00:00')).year
                repo_years.append(year)
            except:
                pass

    if repo_years:
        year_counts = Counter(repo_years)
        print()
        print('=== REPOSITORY CREATION BY YEAR ===')
        for year in sorted(year_counts.keys()):
            print(f'{year}: {year_counts[year]} repositories')
    
    # Repository sizes and activity
    print()
    print('=== REPOSITORY DETAILS ===')
    archived_count = sum(1 for repo in repos if repo.get('is_archived', False))
    with_issues = sum(1 for repo in repos if repo.get('has_issues', False))
    
    print(f'Archived Repositories: {archived_count}')
    print(f'Repositories with Issues enabled: {with_issues}')
    
    # Find most recent repositories
    recent_repos = []
    for repo in repos:
        if 'created_at' in repo:
            try:
                created_date = datetime.fromisoformat(repo['created_at'].replace('Z', '+00:00'))
                recent_repos.append((repo['name'], created_date))
            except:
                pass
    
    recent_repos.sort(key=lambda x: x[1], reverse=True)
    
    print()
    print('=== MOST RECENT REPOSITORIES ===')
    for name, date in recent_repos[:10]:
        print(f'{name}: {date.strftime("%Y-%m-%d")}')

if __name__ == '__main__':
    analyze_github_data()
