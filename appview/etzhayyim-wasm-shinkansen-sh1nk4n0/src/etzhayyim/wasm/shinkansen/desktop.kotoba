(ns etzhayyim.wasm.shinkansen.desktop
  "Reagent mount point for the shinkansen appview page."
  (:require [reagent.dom.client :as rdc]
            [etzhayyim.wasm.shinkansen.ui :as ui]))

(defonce root (rdc/create-root (js/document.getElementById "app")))

(defn ^:dev/after-load render! []
  (rdc/render root [ui/app-view nil]))

(defn init! []
  (render!))
