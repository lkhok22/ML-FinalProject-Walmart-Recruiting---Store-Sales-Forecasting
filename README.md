Walmart Recruiting - Store Sales Forecasting

პროექტის მიმოხილვა

ეს პროექტი წარმოადგენს Kaggle-ის კონკურსს „Walmart Recruiting - Store Sales Forecasting“, სადაც მიზანია Walmart-ის მაღაზიების ყოველკვირეული გაყიდვების პროგნოზირება სხვადასხვა მაღაზიისა და განყოფილებისთვის. პროექტის ფარგლებში გამოცდილია სხვადასხვა მოდელის არქიტექტურა, მათ შორის XGBoost, RandomForest და Prophet, ხოლო ARIMA-ს გამოყენებაც განხილულია, თუმცა გარკვეული შეზღუდვებით.

გამოყენებული მიდგომები

1. მონაცემთა წინასწარი დამუშავება





მონაცემთა შეერთება: train.csv, features.csv, stores.csv ფაილები გაერთიანდა ერთ მონაცემთა ნაკრებად.



თარიღის კონვერტაცია: თარიღის ველები გარდაიქმნა datetime ფორმატში.



ფუნქციების შექმნა:





დამატებულია დროის ფუნქციები: წელი, თვე, კვირა, დღე, სადღესასწაულო დღეების სიახლოვე (მაგ., ნოემბრის მადლიერების დღე, შობა).



Markdown-ის მონაცემების ნაკლოვანებების აღნიშვნა და შევსება (SimpleImputer-ით ან მედიანით).



Lag და Rolling Mean ფუნქციების დამატება გაყიდვების ისტორიის გათვალისწინებით (მაგ., Sales_Lag_1, Sales_Rolling_Mean_3).



კატეგორიული ცვლადები: Type ცვლადი გარდაიქმნა რიცხვით ფორმატში (OrdinalEncoder-ით).

2. მოდელის არქიტექტურები

XGBoost





აღწერა: გამოყენებულია XGBRegressor ჰიპერპარამეტრების ოპტიმიზაციით Optuna-ს გამოყენებით. TimeSeriesSplit გამოყენებულია დროის სერიების სპეციფიკის გათვალისწინებით.



ფუნქციები: Store_Dept ინტერაქციის ფუნქცია, სადღესასწაულო დღეების სიახლოვის ფუნქციები, Lag და Rolling Mean.



ჰიპერპარამეტრები:





n_estimators: 50



max_depth: 6



learning_rate: 0.010558594508774749



subsample: 0.9368769091423239



colsample_bytree: 0.8746968410442786



min_child_weight: 2



reg_alpha: 0.18504540374831363



reg_lambda: 0.16537426759935459



შედეგები:





Validation MAE: 14431.84



Validation WMAE: 14150.16



MLflow: მოდელი შენახულია /kaggle/working/models/xgboost_optimized.joblib სახით MLflow-ის არტიფაქტად.

RandomForest





აღწერა: გამოყენებულია RandomForestRegressor მოდელი, რომელიც მოიცავს ფუნქციების წინასწარ დამუშავებას (Pipeline-ის მეშვეობით).



ფუნქციები: Lag (Sales_Lag_1, Sales_Lag_2) და Rolling Mean (3 და 5 კვირის), სადღესასწაულო ფუნქციები.



ჰიპერპარამეტრები:





n_estimators: 100



max_depth: 20



შედეგები:





Validation MAE: 16105.53



Validation WMAE: 16339.83



MLflow: მოდელი შენახულია /kaggle/working/models/walmart_sales_pipeline.pkl სახით.

Prophet





აღწერა: Prophet მოდელი გამოყენებულია დროის სერიების პროგნოზირებისთვის თითოეული Store-Dept წყვილისთვის ცალ-ცალკე.



ფუნქციები: გამოყენებულია მხოლოდ თარიღი და გაყიდვები, სადღესასწაულო ფუნქციები ავტომატურად განისაზღვრა (yearly_seasonality=True).



შედეგები:





Validation MAE: 1560.97 (ერთი Store-Dept წყვილისთვის)



RMSE: 2011.22



შენიშვნა: Prophet-ის სრული submission ფაილი გენერირდა ყველა Store-Dept წყვილისთვის, თუმცა მოდელის სიზუსტე ნაკლებია XGBoost-თან შედარებით.

ARIMA





აღწერა: გამოყენებულია ARIMA მოდელი (order=(5,1,0)) თითოეული Store-Dept წყვილისთვის.



ფუნქციები: მინიმალური ფუნქციები (Date, Store, Dept), დამატებულია სინუსოიდური ფუნქციები (sin_13, cos_13, sin_23, cos_23).



შეზღუდვები: გარკვეული Store-Dept წყვილებზე (მაგ., (2, 77), (3, 47)) მოდელის ფიტინგი ვერ მოხერხდა LU ან Schur decomposition-ის შეცდომების გამო.



შედეგები: არ ყოფილა სრულად გამოცდილი ვალიდაციის მეტრიკებზე, მაგრამ მოდელი შენახულია arima_pipeline.pkl სახით.



MLflow: მოდელი შენახულია MLflow-ის არტიფაქტად.

3. მოდელის შეფასება





XGBoost: აჩვენა საუკეთესო შედეგები (WMAE: 14150.16), განსაკუთრებით Optuna-ს ოპტიმიზაციის შემდეგ.



RandomForest: ოდნავ ნაკლები სიზუსტე (WMAE: 16339.83), მაგრამ სტაბილური მოდელი.



Prophet: კარგად მუშაობს მცირე მონაცემთა ნაკრებებზე, მაგრამ ნაკლებად ზუსტია ზოგადად.



ARIMA: შეზღუდვების გამო (მონაცემთა ნაკლებობა ან შეცდომები) ნაკლებად ეფექტური იყო.

4. MLflow ინტეგრაცია





ყველა მოდელის ექსპერიმენტი ჩაიწერა MLflow-ში, სადაც შენახულია ჰიპერპარამეტრები, მეტრიკები (MAE, WMAE) და მოდელის არტიფაქტები.



XGBoost: /kaggle/working/models/xgboost_optimized.joblib



RandomForest: /kaggle/working/models/walmart_sales_pipeline.pkl



ARIMA: arima_pipeline.pkl

5. Submission





საბოლოო submission ფაილი (submission.csv) გენერირდა XGBoost-ის საუკეთესო მოდელის გამოყენებით და აიტვირთა Kaggle-ზე.

ფაილები რეპოზიტორში





model_experiment_XGBoost.ipynb: XGBoost მოდელის ექსპერიმენტები და ჰიპერპარამეტრების ოპტიმიზაცია.



model_experiment_RandomForest.ipynb: RandomForest მოდელის ექსპერიმენტები.



model_experiment_Prophet.ipynb: Prophet მოდელის ექსპერიმენტები.



model_experiment_ARIMA.ipynb: ARIMA მოდელის ექსპერიმენტები.



model_inference.ipynb: საბოლოო პროგნოზების გენერირება XGBoost-ის საუკეთესო მოდელის გამოყენებით.



README.md: პროექტის აღწერა (ეს ფაილი).



submission.csv: Kaggle-ის submission ფაილი.

გამოყენებული ინსტრუმენტები





MLflow: ექსპერიმენტების თვალყურის დევნება და მოდელის შენახვა.



Optuna: ჰიპერპარამეტრების ოპტიმიზაცია (XGBoost-ისთვის).



Pandas/NumPy/Sklearn: მონაცემთა დამუშავება და მოდელის მართვა.



XGBoost/RandomForest/Prophet/ARIMA: მოდელის აგება.


