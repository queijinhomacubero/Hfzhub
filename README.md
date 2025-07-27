Scripts locais = {
	{
		LocaisIds = {2753915549, 4442272183, 7449423635},
		UrlPath = "BloxFruits.luau"
	},
	{
		LocaisIds = {10260193230},
		UrlPath = "MemeSea.luau"
	},
	{
		LocaisIds = {104067066727140},
		UrlPath = "VoxSeas.luau"
	}
}

buscador local, urls = {}, {}

local _ENV = (getgenv ou getrenv ou getfenv)()

urls.Owner = "https://raw.githubusercontent.com/tlredz/";
urls.Repository = urls.Owner .. "Scripts/refs/heads/main/";
urls.Translator = urls.Repository .. "Tradutor/";
urls.Utils = urls.Repositório .. "Utils/";

fazer
	último_exec local = _ENV.rz_execute_debounce
	
	se last_exec e (tick() - last_exec) <= 5 então
		retornar nulo
	fim
	
	_ENV.rz_execute_debounce = tick()
fim

fazer
	executor local = syn ou fluxus
	local queueteleport = queue_on_teleport ou (executor e executor.queue_on_teleport)
	
	se não for _ENV.rz_added_teleport_queue e type(queueteleport) == "function" então
		Configurações de script locais = {...}
		Código de configuração local = ""
		
		_ENV.rz_added_teleport_queue = verdadeiro
		
		Sucesso local, EncodedSettings = pcall(function()
			retornar jogo:GetService("HttpService"):JSONEncode(ScriptSettings)
		fim)
		
		se Sucesso e EncodedSettings então
			SettingsCode = "descompactar(jogo:GetService('HttpService'):JSONDecode('" .. EncodedSettings .. "'))"
		fim
		
		pcall(queueteleport, ("loadstring(jogo:HttpGet('%smain.luau'))(%s)"):format(urls.Repository, SettingsCode))
	fim
fim

fazer
	se _ENV.rz_error_message então
		_ENV.rz_error_message:Destruir()
	fim
	
	local identifyexecutor = identifyexecutor ou (função() retornar "Desconhecido" fim)
	
	função local CreateMessageError(Texto)
		_ENV.loadedFarm = nulo
		_ENV.OnFarm = falso
		
		Mensagem local = Instance.new("Mensagem", espaço de trabalho)
		Mensagem.Texto = string.gsub(Texto, urls.Proprietário, "")
		_ENV.rz_error_message = Mensagem
		
		erro(Texto, 2)
	fim
	
	função local formatUrl(Url)
		para chave, caminho em urls faça
			se Url:find("{" .. chave .. "}") então
				retornar Url:gsub("{" .. chave .. "}", caminho)
			fim
		fim
		
		retornar URL
	fim
	
	função fetcher.get(Url)
		sucesso local, resposta = pcall(function()
			retornar jogo:HttpGet(formatUrl(Url))
		fim)
		
		se sucesso então
			resposta de retorno
		outro
			CreateMessageError(`[1] [{ identifyexecutor() }] falhou ao obter http/url/raw: { Url }\n>>{ resposta }<<`)
		fim
	fim
	
	função fetcher.load(Url: string, concat: string?)
		local raw = fetcher.get(Url) .. (se concat então concat senão "")
		Função de execução local, errorText = loadstring(raw)
		
		se tipo(runFunction) ~= "função" então
			CreateMessageError(`[2] [{ identifyexecutor() }] erro de sintaxe: { Url }\n>>{ errorText }<<`)
		outro
			retornar função de execução
		fim
	fim
fim

função local IsPlace(Script)
	se Script.PlacesIds e table.find(Script.PlacesIds, game.PlaceId) então
		retornar verdadeiro
	elseif Script.GameId e Script.GameId == game.GameId então
		retornar verdadeiro
	fim
fim

para _, Script em Scripts do
	se IsPlace(Script) então
		retornar fetcher.load("{Repositório}Jogos/" .. Script.UrlPath)(buscador, ...)
	fim
fim
