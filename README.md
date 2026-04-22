<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ayoub Nechat | Data Analyst</title>
    <style>
        :root { --bg: #ffffff; --text: #1a1a1a; --gray: #666; --accent: #2563eb; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; line-height: 1.6; color: var(--text); margin: 0; padding: 40px 20px; }
        .container { max-width: 800px; margin: 0 auto; }
        h1 { font-size: 2.5rem; margin-bottom: 0.5rem; }
        h2 { font-size: 1.8rem; margin-top: 3rem; border-bottom: 2px solid #eee; padding-bottom: 10px; }
        h3 { font-size: 1.3rem; margin-top: 2rem; color: var(--accent); }
        .intro { color: var(--gray); font-size: 1.2rem; margin-bottom: 2rem; }
        code { background: #f4f4f4; padding: 2px 5px; border-radius: 4px; font-family: monospace; }
        table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
        th { background-color: #f8f8f8; }
        .footer { margin-top: 4rem; text-align: center; color: var(--gray); font-size: 0.9rem; }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>Ayoub Nechat</h1>
        <p class="intro">Data Analyst | Portfolio : Alpitrail Sales Performance Analytics</p>
    </header>

    <section>
        <h2>📌 Contexte business</h2>
        <p>Alpitrail est une marque fictive d'équipements outdoor opérant en Europe, Amérique du Nord et Asie. L'objectif est d'optimiser la rentabilité par région, catégorie et segment client.</p>
    </section>

    <section>
        <h2>📊 Dataset</h2>
        <table>
            <tr><th>Indicateur</th><th>Valeur</th></tr>
            <tr><td>Transactions</td><td>3 050 lignes</td></tr>
            <tr><td>Clients CRM</td><td>1 500</td></tr>
            <tr><td>CA total simulé</td><td>~4,86 M$</td></tr>
        </table>
    </section>

    <section>
        <h2>🔄 Étapes clés</h2>
        <h3>1. Nettoyage (Python)</h3>
        <p>Actions : Suppression des doublons, traitement des outliers (méthode IQR), normalisation et calcul de KPIs (revenue, gross_profit, margin).</p>
        
        <h3>2. Analyse SQL</h3>
        <p>Requêtes complexes utilisant <code>GROUP BY</code>, <code>RANK() OVER (PARTITION BY)</code> pour l'analyse de saisonnalité et <code>CASE WHEN</code> pour l'évolution YoY.</p>
        
        <h3>3. Machine Learning</h3>
        <p>Modélisation avec <code>RandomForestRegressor</code> pour prédire <code>units_sold</code>. Évaluation via RMSE et R².</p>
    </section>

    <section>
        <h2>💡 Insights principaux</h2>
        <ul>
            <li>L'Asie est le marché le plus dynamique (+15% de CA).</li>
            <li>Les "Jackets" affichent la plus forte croissance (+40%).</li>
            <li>Le segment SME représente 60% des clients.</li>
        </ul>
    </section>

    <footer class="footer">
        <p>© 2026 Ayoub Nechat | <a href="https://github.com/ton-pseudo">GitHub</a></p>
    </footer>
</div>

</body>
</html>
