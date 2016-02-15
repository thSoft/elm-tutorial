# Fetching perks

For __perks__ and __perksPlayers__ we will follow the same pattern as players. 

## Perks Models

Add __src/Perks/Models.elm__:

```elm
module Perks.Models (..) where


type alias PerkId =
  Int


type alias Perk =
  { id : PerkId
  , name : String
  , bonus : Int
  , description : String
  }
```

## Perks Actions

__src/Perks/Actions.elm__:

```elm
module Perks.Actions (..) where

import Http
import Perks.Models exposing (Perk)


type Action
  = NoOp
  | FetchAll
  | FetchAllDone (Result Http.Error (List Perk))
  | TaskDone ()
```

## Perks Effects

__src/Perks/Effects.elm__:

```elm
module Perks.Effects (..) where

import Effects exposing (Effects)
import Http
import Json.Decode as Decode exposing ((:=))
import Task
import Perks.Models exposing (Perk)
import Perks.Actions as Actions


fetchAll : Effects Actions.Action
fetchAll =
  Http.get collectionDecoder fetchAllUrl
    |> Task.toResult
    |> Task.map Actions.FetchAllDone
    |> Effects.task


fetchAllUrl : String
fetchAllUrl =
  "http://localhost:4000/perks"



-- DECODERS


collectionDecoder : Decode.Decoder (List Perk)
collectionDecoder =
  Decode.list memberDecoder


memberDecoder : Decode.Decoder Perk
memberDecoder =
  Decode.object4
    Perk
    ("id" := Decode.int)
    ("name" := Decode.string)
    ("bonus" := Decode.int)
    ("description" := Decode.string)
```

## Perks Update

__src/Perks/Update.elm__:

```elm
module Perks.Update (..) where

import Effects exposing (Effects)
import Perks.Actions exposing (..)
import Perks.Models exposing (Perk)


type alias UpdateModel =
  { perks : List Perk
  , showErrorAddress : Signal.Address String
  }


update : Action -> UpdateModel -> ( List Perk, Effects Action )
update action model =
  case action of
    FetchAllDone result ->
      case result of
        Ok perks ->
          ( perks, Effects.none )

        Err error ->
          let
            message =
              toString error

            fx =
              Signal.send model.showErrorAddress message
                |> Effects.task
                |> Effects.map TaskDone
          in
            ( model.perks, fx )

    _ ->
      ( model.perks, Effects.none )
```

## PerksPlayers Models

__/src/PerksPlayers/Models.elm__:

```elm
module PerksPlayers.Models (..) where

import Players.Models exposing (PlayerId)
import Perks.Models exposing (PerkId)


type alias PerkPlayerId =
  Int


type alias PerkPlayer =
  { id : PerkPlayerId
  , perkId : PerkId
  , playerId : PlayerId
  }

```

## PerksPlayers Actions

## PerksPlayers Effects

## PerksPlayers Update

## Main Actions

## Main Models

## Main Update

## Main

---



