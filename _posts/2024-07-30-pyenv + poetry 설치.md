
# 개요

## 크롬북에 pyenv + poetry 설치하기

크롬북 환경이라고 linux와 별 다를 것 없지만 진행한 내용 정리. (어차피 crostini가 debian 계열이라..)

라이센스 사정상 conda는 이제는 사용하지 않고 디폴트로 사용가능한 venv 위주로 사용하는 중이었는데 요즘 대세인 pyenv + poetry를 확인해 봄.

- pyenv 
  - 여러 python 버젼을 설치하고 관리하는 도구. (forked from rbenv for python.)
- poetry
  - 프로젝트의 의존성 관리와 패키지 배포 도구.
  - pip, virtualenv 대체. (무의식적으로 pip install 하지 말아야 함)
  - pyproject.toml과 poetry.lock 사용. 


## 참고 
- [https://github.com/pyenv/pyenv](https://github.com/pyenv/pyenv)
- [https://python-poetry.org/](https://python-poetry.org/)  
- [https://velog.io/@euisuk-chung/Goodbye-Conda-Hello-PyENV](https://velog.io/@euisuk-chung/Goodbye-Conda-Hello-PyENV)
- [https://teddylee777.github.io/poetry/poetry-tutorial/](https://teddylee777.github.io/poetry/poetry-tutorial/)





# pyenv

## 설치 
``` bash
curl https://pyenv.run | bash
```

## .bashrc 추가 
각각 env 설정, path 설절, pyenv 초기화, virtualenv라는 플러그인 초기화. (마지막은 옵션)
``` bash
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

## pyenv 버젼 확인
``` bash
pyenv --version

pyenv install --list
```

## 파이썬 버젼 설치/삭제 
``` bash

pyenv install 3.8 # 3.8의 latest는 3.8.19

pyenv uninstall 3.8.19
```

## 파이썬 버전들 확인 (현재)
``` bash
pyenv versions 
```

## 파이썬 버젼 global 설정
``` bash
pyenv global 3.11
```

## 파이썬 버젼 확인
``` bash
python --version
```

# poetry

## 설치 
``` bash
pip install poetry
```

## 기존 설정 사용
``` bash
cd working_dir # git clone dir
poetry shell # 가상 환경 실행
poetry install # poetry.lock 참고하여 패키지 설치 
```

## 새로 만들기
``` bash
cd new_working_dir
poetry init # wizard 진행후 pyproject.toml 생성됨
```
혹은
``` bash
poetry new 프로젝트  
```

``` bash
$ poetry new asdf
$ tree
.
├── asdf
│   └── __init__.py
├── pyproject.toml
├── README.md
└── tests
    └── __init__.py

3 directories, 4 files
```

## 가상 환경 명령어
``` bash
poetry shell # 시작

deactivate # 종료 

poetry env info # 확인

poetry env list # 목록 

poetry env remove <venv> # 삭제 
```


## 패키지 추가/삭제/확인/내보내기 등등 
``` bash
poetry add faker

poetry add --dev faker # dev dependency 

poetry remove faker

poetry show
poetry show --tree
poetry show --latest
poetry show --outdated

poetry export -f requirements.txt --output requirements.txt
```



## 한번에 실행하기 
``` bash
poetry run myapp.py
```

## 빌드 및 배포 
``` bash
poetry build

poetry publish 
```








