import requests
import json
import pandas as pd

api_token = '3AaCxOM8TW7jUxLecTGCUJzMwggyarKa5HWuxQjZ'
auth_url = 'https://demo-eu-1.leanix.net/services/mtm/v1/oauth2/token'
request_url = 'https://demo-eu-1.leanix.net/services/pathfinder/v1/graphql'

response = requests.post(auth_url, auth=('apitoken', api_token),
                         data={'grant_type': 'client_credentials'})
response.raise_for_status()
access_token = response.json()['access_token']

application_id = "08aad314-f3ef-4b16-9347-46ad42979e67"

graphql_query = """{
  factSheet(id: "%s") {
    ... on Application {
      relApplicationToProcess {
    edges {
          node {
factSheet{
              displayName
              ... on Process{
                CGWesentlich
                CGVIVAProzVertrau
                CGVIVAProzIntegri
                CGVivaProzVerf
                CGVivaProzAuthen
                  }
                }
              }
            }
          }
        }
    }
}""" % (application_id)

data = {"query": graphql_query}
json_data = json.dumps(data)
auth_header = 'Bearer ' + access_token
header = {'Authorization': auth_header}

response = requests.post(url=request_url, headers=header, data=json_data)
response.raise_for_status()

# data_frame = pd.json_normalize(response.json())
data_frame = pd.json_normalize(response.json()['data']['factSheet']['relApplicationToProcess']['edges'])
df = data_frame[data_frame.columns]


# v = data_frame.relApplicationToProcess.edges
# print(v)

# data_frame.to_excel('my_graphql_data.xlsx', index=False)
# print(data_frame)


# print(df)

# print(df['node.factSheet.displayName'][1])
# cols = list(df.columns)
# print(cols)

# rows = list(df.values)
# print(rows)


# process = df['node.factSheet.displayName']
# print(process)

def call(query):
    data = {"query": query}
    json_data = json.dumps(data)
    response = requests.post(url=request_url, headers=header, data=json_data)
    response.raise_for_status()
    return response.json()


def compute_score(strength):
    match strength:
        case 'Sehr_hoch':
            return 7
        case 'Hoch':
            return 5
        case 'Mittel':
            return 3
        case 'Niedrig':
            return 1


def compute_strength(score):
    match score:
        case 7:
            return 'Sehr_hoch'
        case 5:
            return 'Hoch'
        case 3:
            return 'Mittel'
        case 1:
            return 'Niedrig'
        case default:
            return "None"


essential = []
score_CGVIVAProzVertrau = 0
strength_CGVIVAProzVertrau = 0
score_CGVIVAProzIntegri = 0
strength_CGVIVAProzIntegri = 0
score_CGVivaProzVerf = 0
strength_CGVivaProzVerf = 0
score_CGVivaProzAuthen = 0
strength_CGVivaProzAuthen = 0
strength_CGWesentlich = 'Nein'

for row in range(len(list(df.values))):
    if (df['node.factSheet.CGWesentlich'][row] == 'Ja'):

        strength_CGWesentlich = 'Ja'

        score_CGVIVAProzVertrau += compute_score(df['node.factSheet.CGVIVAProzVertrau'][row])
        if (strength_CGVIVAProzVertrau < compute_score(df['node.factSheet.CGVIVAProzVertrau'][row])):
            strength_CGVIVAProzVertrau = compute_score(df['node.factSheet.CGVIVAProzVertrau'][row])

        score_CGVIVAProzIntegri += compute_score(df['node.factSheet.CGVIVAProzIntegri'][row])
        if (strength_CGVIVAProzIntegri < compute_score(df['node.factSheet.CGVIVAProzIntegri'][row])):
            strength_CGVIVAProzIntegri = compute_score(df['node.factSheet.CGVIVAProzIntegri'][row])

        score_CGVivaProzVerf += compute_score(df['node.factSheet.CGVivaProzVerf'][row])
        if (strength_CGVivaProzVerf < compute_score(df['node.factSheet.CGVivaProzVerf'][row])):
            strength_CGVivaProzVerf = compute_score(df['node.factSheet.CGVivaProzVerf'][row])

        score_CGVivaProzAuthen += compute_score(df['node.factSheet.CGVivaProzAuthen'][row])
        if (strength_CGVivaProzAuthen < compute_score(df['node.factSheet.CGVivaProzAuthen'][row])):
            strength_CGVivaProzAuthen = compute_score(df['node.factSheet.CGVivaProzAuthen'][row])

Total_Viva_Score = score_CGVIVAProzVertrau + score_CGVIVAProzIntegri + score_CGVivaProzVerf + score_CGVivaProzAuthen


        # print(score_CGVIVAProzVertrau)
        #essential.append(df.values[row])


# print(strength_CGVIVAProzIntegri)

# print('Essential Process: \n', essential)
# print('\n\n')
#
print('score_CGVIVAProzVertrau = ', score_CGVIVAProzVertrau, '\nscore_CGVIVAProzIntegri = ', score_CGVIVAProzIntegri,
      '\nscore_CGVivaProzVerf = ', score_CGVivaProzVerf, '\nscore_CGVivaProzAuthen = ', score_CGVivaProzAuthen)

#print('\n\n')

print('strength_CGWesentlich = ', strength_CGWesentlich,
      '\nstrength_CGVIVAProzVertrau = ', compute_strength(strength_CGVIVAProzVertrau),
      '\nstrength_CGVIVAProzIntegri = ', compute_strength(strength_CGVIVAProzIntegri),
      '\nstrength_CGVivaProzVerf = ', compute_strength(strength_CGVivaProzVerf),
      '\nstrength_CGVivaProzAuthen = ', compute_strength(strength_CGVivaProzAuthen))
# #
# print('\n\n')
print('Total_Viva_Score = ', Total_Viva_Score)

mutation_string = """
    mutation{
  updateFactSheet(id: "%s", 
     patches: [{op:replace, path:"/CGVIVA", value:"%s"},
     {op:replace, path:"/CGVIVAVertrau", value:"%s"},
     {op:replace, path:"/CGVIVAIntegri", value:"%s"},
     {op:replace, path:"/CGVIVAVerf", value:"%s"},
     {op:replace, path:"/CGVIVAAuthen", value:"%s"},
     {op:replace, path:"/CGGeschaeftskrit", value:"%s"},
     {op:replace, path:"/CGVIVAVertrauMax", value:"%s"},
     {op:replace, path:"/CGVIVAIntegriMax", value:"%s"},
     {op:replace, path:"/CGVIVAVerfMaximum", value:"%s"},
     {op:replace, path:"/CGVIVAAuthenMaximum", value:"%s"}
     ]) {
    factSheet {
      ... on Application {
        CGVIVA
        CGVIVAVertrau
        CGVIVAIntegri
        CGVIVAVerf
        CGVIVAAuthen
        CGGeschaeftskrit
        CGVIVAVertrauMax
        CGVIVAIntegriMax
        CGVIVAVerfMaximum
        CGVIVAAuthenMaximum
      }
    }
  }
}
  """ % (application_id, Total_Viva_Score, score_CGVIVAProzVertrau, score_CGVIVAProzIntegri, score_CGVivaProzVerf, score_CGVivaProzAuthen, strength_CGWesentlich, compute_strength(strength_CGVIVAProzVertrau) , compute_strength(strength_CGVIVAProzIntegri), compute_strength(strength_CGVivaProzVerf), compute_strength(strength_CGVivaProzAuthen))

data_mutation = {"query": mutation_string}
json_post = json.dumps(data_mutation)
auth_header = 'Bearer ' + access_token
header = {'Authorization': auth_header}
response = requests.post(url=request_url, headers=header, data=json_post)
response.raise_for_status()
