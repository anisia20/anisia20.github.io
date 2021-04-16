### example
    file -> setting -> project interpreter
    requests beautifulsoup4 설치


    importpandasaspd

    #html에있는정보를읽어온다.
    #header=0으로맨윗줄의데이터를헤더로사용하고얻은자료를리스트형태로이용하기위해뒤에[0]을붙여준다.
    code_df=pd.read_html('http://kind.krx.co.kr/corpgeneral/corpList.do?method=download&searchType=13',header=0)[0]

    #타입을확인
    print(type(code_df))#<class'pandas.core.frame.DataFrame'>
    print(code_df.head())#데이터를확인

    #code_df에있는'종목코드'컬럼을0을채운6자리포멧으로맞춰준다.
    code_df.종목코드=code_df.종목코드.map('{:06d}'.format)

    #code_df를회사명과종목코드컬럼만뽑아낸다.
    #***참고***pandas에서컬럼을선택할때
    #단일개선택:df['컬럼명']ordf.컬럼명
    #여러개선택:df[['컬럼명',...,'컬럼명']]

    code_df=code_df[['회사명','종목코드']]
    print(code_df)#데이터를확인

    #한글로된컬럼명을영어로바꿔준다.
    code_df=code_df.rename(columns={'회사명':'name','종목코드':'code'})

    #신라젠네이버금융주소.http://finance.naver.com/item/main.nhn?code=215600
    #함수생성=>특정한업체만코드를가져오기위해서

    defget_url(item_name,code_df):
    #코드를가져오기위한처리.
    #먼저.query("name=='{}'".format(item_name))['code']는name컬럼에item_name과동일한값의code값을반환한다는뜻.
    #즉,.query("쿼리".format(쿼리에넣을데이터))[얻을자료]
    #.to_string(index=False)로위에서얻어진값에index를빼고string타입으로바꿔준다.
    code=code_df.query("name=='{}'".format(item_name))['code'].to_string(index=False)

    #url은일일종가시가고가저가거래량을보여주는표이다.
    url='http://finance.naver.com/item/sise_day.nhn?code={code}'.format(code=code)
    url=url.replace("","")
    print("요청URL={}".format(url))
    returnurl



    #신라젠정보가져오기
    item_name='신라젠'
    url=get_url(item_name,code_df)
    df=pd.DataFrame()

    #크롤링.페이지20까지크롤링을한다.
    forpageinrange(1,21):
    #위에서얻은url에page를붙여줘서url포멧을만들어준다.
    pg_url='{url}&page={page}'.format(url=url,page=page)
    #pandas의df에위에서얻은url을넣어줘서우리가구하고자하는데이터프레임을만든다.
    #데이터프레임을만들때리스트에[0]을붙여줘서만들수있음을다시확인.
    df=df.append(pd.read_html(pg_url,header=0)[0],ignore_index=True)

    #df.dropna()를이용해결측값(NaN)있는행을제거한다.
    df=df.dropna()

    #상위5개데이터확인하기
    print(df.head())

    #한글로된컬럼명을영어로바꿔준다.
    df=df.rename(columns={'날짜':'date','종가':'close','전일비':'diff',
    '시가':'open','고가':'high','저가':'low','거래량':'volume'})

    #데이터의타입을int형으로바꿔줌.\(역슬래쉬)는뒤에데이터가이어진다는의미이다.한줄로쓰면\필요없음.
    df[['close','diff','open','high','low','volume']]=df[['close','diff','open','high','low','volume']].astype(int)

    #컬럼명'date'의타입을date로바꿔줌
    df['date']=pd.to_datetime(df['date'])

    #일자(date)를기준으로오름차순정렬
    df=df.sort_values(by=['date'],ascending=True)

    #상위5개데이터확인
    print(df.head())

