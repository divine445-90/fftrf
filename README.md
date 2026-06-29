# fftrf
dd
// Save as src/main/java/com/example/uncertainhorror/Main.java
package com.example.uncertainhorror;


import javafx.animation.KeyFrame;
import javafx.animation.Timeline;
import javafx.application.Application;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Cursor;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.effect.*;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.input.KeyCode;
import javafx.scene.layout.*;
import javafx.scene.media.AudioClip;
import javafx.scene.paint.Color;
import javafx.scene.text.Font;
import javafx.stage.Stage;
import javafx.util.Duration;


import java.util.HashMap;
import java.util.Map;


/**
 * Enhanced atmospheric prototype with detailed dialogue variants, red herrings,
 * analog/VHS overlay, ambient music and layered creepy SFX.
 *
 * Assets expected in src/main/resources/assets/:
 * - ep1.jpg ... ep5.jpg
 * - ambience_loop.mp3
 * - whisper1.mp3, whisper2.mp3
 * - knock.mp3
 * - sting.mp3
 * - vhs_static.png (transparent static overlay)
 * - scanlines.png (transparent horizontal lines)
 *
 * Replace placeholders with licensed assets and keep license records.
 */
public class Main extends Application {


    private Stage primaryStage;
    private Map<Integer, Episode> episodes = new HashMap<>();
    private int metaFragments = 0;


    // Audio layers
    private AudioClip ambience;
    private AudioClip whisper1;
    private AudioClip whisper2;
    private AudioClip knock;
    private AudioClip sting;
    private AudioClip vhsHum;


    // Low intensity toggle
    private boolean lowIntensity = false;


    public static void main(String[] args) {
        launch(args);
    }


    @Override
    public void start(Stage primaryStage) {
        this.primaryStage = primaryStage;
        loadAudio();
        buildEpisodesWithDialogue();
        primaryStage.setTitle("Uncertain Nights — Atmospheric Prototype");
        showLanding();
        primaryStage.show();
    }


    private void loadAudio() {
        try { ambience = new AudioClip(getClass().getResource("/assets/ambience_loop.mp3").toExternalForm()); } catch (Exception e) { ambience = null; }
        try { whisper1 = new AudioClip(getClass().getResource("/assets/whisper1.mp3").toExternalForm()); } catch (Exception e) { whisper1 = null; }
        try { whisper2 = new AudioClip(getClass().getResource("/assets/whisper2.mp3").toExternalForm()); } catch (Exception e) { whisper2 = null; }
        try { knock = new AudioClip(getClass().getResource("/assets/knock.mp3").toExternalForm()); } catch (Exception e) { knock = null; }
        try { sting = new AudioClip(getClass().getResource("/assets/sting.mp3").toExternalForm()); } catch (Exception e) { sting = null; }
        try { vhsHum = new AudioClip(getClass().getResource("/assets/vhs_hum.mp3").toExternalForm()); } catch (Exception e) { vhsHum = null; }
    }


    private void buildEpisodesWithDialogue() {
        // Episode 1: Home Invasion — dialogue variants and red herrings
        episodes.put(1, new Episode(
                1,
                "Home Invasion",
                "Late night. Small sounds feel amplified. The house seems to breathe.",
                "/assets/ep1.jpg",
                new String[]{"Check the door and lock it", "Ignore and go inside"},
                (choice, ctx) -> {
                    if ("Check the door and lock it".equals(choice)) {
                        ctx.dialogue = new String[]{
                                "You (quiet): 'Just lock it. Quick and done.'",
                                "Phone buzzes: 'Did you lock the back? I left my keys.'",
                                "Neighbour (distant): 'Sorry, wrong house.'",            // red herring
                                "You (to yourself): 'Better safe than sorry.'"
                        };
                        metaFragments++;
                        return new Outcome("Locked", "You lock the door. The knock fades. You feel the tension ease.", false);
                    } else {
                        ctx.dialogue = new String[]{
                                "You (muttering): 'Probably the delivery.'",
                                "A muffled voice outside: 'Hey, mate, you in there?'",   // ambiguous threat
                                "You (hesitant): 'Yeah, just a sec.'",
                                "A second voice, far: 'Is that Sam?' (could be neighbour or stranger)" // red herring
                        };
                        metaFragments++;
                        return new Outcome("Intruder", "You open the door. A stranger slips inside — tense but non-graphic.", true);
                    }
                }
        ));


        // Episode 2: Drunk Driving — dialogue variants and red herrings
        episodes.put(2, new Episode(
                2,
                "Drunk Driving",
                "A party dissolves into a decision. The car ride is a corridor of bad choices.",
                "/assets/ep2.jpg",
                new String[]{"Accept the lift", "Call a rideshare"},
                (choice, ctx) -> {
                    if ("Call a rideshare".equals(choice)) {
                        ctx.dialogue = new String[]{
                                "Friend: 'You sure? It's late and the trains stopped.'",
                                "You: 'I'll wait. I'll call a ride.'",
                                "Someone laughs nearby: 'You always overthink, Sam.'", // red herring: social pressure
                                "You (relieved): 'Better safe.'"
                        };
                        metaFragments++;
                        return new Outcome("Sober", "You chose sober transport and arrived safely.", false);
                    } else {
                        ctx.dialogue = new String[]{
                                "Driver (slurred): 'C'mon, it's fine. I know the way.'",
                                "You (hesitant): 'Alright... let's go.'",
                                "Tyre screech in the distance — maybe a car, maybe nothing.", // red herring sound
                                "Driver: 'Trust me.'"
                        };
                        metaFragments++;
                        return new Outcome("Crash", "A sudden swerve. Consequences are immediate and heavy.", true);
                    }
                }
        ));


        // Episode 3: Motorcycle Crash — dialogue variants and red herrings
        episodes.put(3, new Episode(
                3,
                "Motorcycle Crash",
                "Speed feels like control until it doesn't. The road narrows and sound sharpens.",
                "/assets/ep3.jpg",
                new String[]{"Wear helmet and ride cautiously", "Skip helmet for the thrill"},
                (choice, ctx) -> {
                    if ("Wear helmet and ride cautiously".equals(choice)) {
                        ctx.dialogue = new String[]{
                                "You (to yourself): 'Helmet on. Eyes open.'",
                                "A rider passes and nods; the road stays steady.",
                                "A distant horn — a truck, or just a horn echoing off the trees?" // red herring
                        };
                        metaFragments++;
                        return new Outcome("Minor", "You swerve and avoid major harm. Protective gear helped.", false);
                    } else {
                        ctx.dialogue = new String[]{
                                "You (grinning): 'Just one run, no helmet.'",
                                "Wind: a high, thin sound. A car door opens ahead.",
                                "A shadow moves at the roadside — maybe an animal, maybe a person." // red herring
                        };
                        metaFragments++;
                        return new Outcome("Crash", "A crash occurs. The scene focuses on consequence and recovery.", true);
                    }
                }
        ));


        // Episode 4: Financial Scam — dialogue variants and red herrings
        episodes.put(4, new Episode(
                4,
                "Financial Scam",
                "A message arrives with urgency. The tone is clinical but persuasive.",
                "/assets/ep4.jpg",
                new String[]{"Click the link", "Verify with official site"},
                (choice, ctx) -> {
                    if ("Verify with official site".equals(choice)) {
                        ctx.dialogue = new String[]{
                                "You: 'I'll check the bank app directly.'",
                                "System: 'No action required.'",
                                "A notification pings from a different app — could be related, could be noise." // red herring
                        };
                        metaFragments++;
                        return new Outcome("Safe", "You verified and avoided the scam.", false);
                    } else {
                        ctx.dialogue = new String[]{
                                "Message (urgent): 'Immediate action required. Click now.'",
                                "You (rushed): 'I'll sort it now.'",
                                "A browser popup appears — looks official, but the URL is slightly off." // red herring that becomes real
                        };
                        metaFragments++;
                        return new Outcome("Loss", "You followed the link and lost money or data.", true);
                    }
                }
        ));


        // Episode 5: Party Risk — dialogue variants and red herrings
        episodes.put(5, new Episode(
                5,
                "Party Risk",
                "A crowded room, a pill offered, laughter that hides unease.",
                "/assets/ep5.jpg",
                new String[]{"Refuse and stay with friends", "Accept to fit in"},
                (choice, ctx) -> {
                    if ("Refuse and stay with friends".equals(choice)) {
                        ctx.dialogue = new String[]{
                                "Friend: 'You good?'",
                                "You: 'Yeah, I'm sticking with you.'",
                                "Someone shouts across the room: 'You missed the best part!' — a distraction, not a threat." // red herring
                        };
                        metaFragments++;
                        return new Outcome("Safe", "You stayed safe and supported your friends.", false);
                    } else {
                        ctx.dialogue = new String[]{
                                "Peer: 'Everyone's doing it, don't be lame.'",
                                "You (quiet): 'Alright.'",
                                "A glass shatters near the doorway — sudden noise that masks a call for help." // red herring that hides real danger
                        };
                        metaFragments++;
                        return new Outcome("Overdose", "A medical emergency occurs; calling for help matters.", true);
                    }
                }
        ));
    }


    private void showLanding() {
        VBox root = new VBox(12);
        root.setPadding(new Insets(18));
        root.setStyle("-fx-background-color: #070709;");
        Label title = new Label("Uncertain Nights");
        title.setFont(Font.font(28));
        title.setTextFill(Color.web("#f6f6f6"));
        Label subtitle = new Label("Five atmospheric episodes. Choices shape consequence.");
        subtitle.setTextFill(Color.web("#bdbdbd"));
        subtitle.setWrapText(true);


        HBox controls = new HBox(10);
        controls.setAlignment(Pos.CENTER_LEFT);
        CheckBox low = new CheckBox("Low intensity mode");
        low.setTextFill(Color.web("#bdbdbd"));
        low.setOnAction(e -> lowIntensity = low.isSelected());
        controls.getChildren().add(low);


        VBox list = new VBox(8);
        for (int i = 1; i <= episodes.size(); i++) {
            Episode ep = episodes.get(i);
            Button btn = new Button(i + ". " + ep.title + " — " + ep.desc);
            btn.setMaxWidth(Double.MAX_VALUE);
            btn.setCursor(Cursor.HAND);
            int idx = i;
            btn.setOnAction(e -> showEpisode(idx));
            list.getChildren().add(btn);
        }


        Label progress = new Label("Fragments collected: " + metaFragments);
        progress.setTextFill(Color.web("#bdbdbd"));


        root.getChildren().addAll(title, subtitle, controls, list, progress);
        Scene scene = new Scene(root, 1000, 700);
        primaryStage.setScene(scene);
    }


    private void showEpisode(int epIndex) {
        Episode ep = episodes.get(epIndex);


        BorderPane root = new BorderPane();
        root.setPadding(new Insets(12));
        root.setStyle("-fx-background-color: #0b0b0b;");


        VBox top = new VBox(6);
        Label title = new Label(epIndex + ". " + ep.title);
        title.setFont(Font.font(22));
        title.setTextFill(Color.web("#f3f3f3"));
        Label desc = new Label(ep.desc);
        desc.setTextFill(Color.web("#cfcfcf"));
        desc.setWrapText(true);
        top.getChildren().addAll(title, desc);
        root.setTop(top);


        // Hero image
        ImageView hero = new ImageView();
        try {
            Image img = new Image(getClass().getResourceAsStream(ep.imagePath));
            hero.setImage(img);
            hero.setFitWidth(760);
            hero.setPreserveRatio(true);
            ColorAdjust adjust = new ColorAdjust();
            adjust.setBrightness(-0.45);
            adjust.setContrast(0.05);
            hero.setEffect(adjust);
        } catch (Exception ex) {
            // missing image is acceptable for prototype
        }
        StackPane center = new StackPane(hero);
        center.setPadding(new Insets(10));


        // Analog overlay: static + scanlines
        try {
            Image staticImg = new Image(getClass().getResourceAsStream("/assets/vhs_static.png"));
            ImageView staticView = new ImageView(staticImg);
            staticView.setFitWidth(760);
            staticView.setPreserveRatio(true);
            staticView.setOpacity(0.12); // adjustable intensity
            staticView.setBlendMode(BlendMode.OVERLAY);


            Image scan = new Image(getClass().getResourceAsStream("/assets/scanlines.png"));
            ImageView scanView = new ImageView(scan);
            scanView.setFitWidth(760);
            scanView.setPreserveRatio(true);
            scanView.setOpacity(0.08);


            // subtle chromatic aberration via color adjust on a duplicate
            ImageView chroma = new ImageView(hero.getImage());
            chroma.setFitWidth(760);
            chroma.setPreserveRatio(true);
            chroma.setTranslateX(1.5);
            chroma.setOpacity(0.06);
            ColorAdjust ca = new ColorAdjust();
            ca.setHue(0.02);
            chroma.setEffect(ca);


            center.getChildren().addAll(chroma, staticView, scanView);
        } catch (Exception ignored) {}


        root.setCenter(center);


        VBox right = new VBox(10);
        right.setPadding(new Insets(10));
        right.setPrefWidth(360);


        TextArea dialogueBox = new TextArea();
        dialogueBox.setEditable(false);
        dialogueBox.setWrapText(true);
        dialogueBox.setPrefRowCount(6);
        dialogueBox.setStyle("-fx-control-inner-background:#0f0f10; -fx-text-fill:#e8e8e8;");


        // Buttons for choices
        for (String choiceLabel : ep.choices) {
            Button c = new Button(choiceLabel);
            c.setMaxWidth(Double.MAX_VALUE);
            c.setOnAction(e -> {
                // stop ambience if playing
                if (ambience != null) ambience.stop();
                Context ctx = new Context();
                Outcome outcome = ep.resolve(choiceLabel, ctx);
                // show dialogue sequence (includes red herrings) then handle tension
                showDialogueSequence(dialogueBox, ctx.dialogue, () -> handleChoiceWithTension(epIndex, outcome, center));
            });
            right.getChildren().add(c);
        }


        Button start = new Button("Begin tension sequence");
        start.setOnAction(e -> startTensionSequence(epIndex, center, dialogueBox));
        Button back = new Button("Back");
        back.setOnAction(e -> {
            if (ambience != null) ambience.stop();
            showLanding();
        });


        right.getChildren().addAll(new Label("Scene dialogue"), dialogueBox, start, back);
        root.setRight(right);


        Scene scene = new Scene(root, 1200, 760);
        scene.setOnKeyPressed(ev -> {
            if (ev.getCode() == KeyCode.ESCAPE) {
                if (ambience != null) ambience.stop();
                showLanding();
            }
        });


        primaryStage.setScene(scene);
    }


    private void startTensionSequence(int epIndex, StackPane centerPane, TextArea dialogueBox) {
        // ambient loop + optional VHS hum
        if (ambience != null) {
            ambience.setCycleCount(AudioClip.INDEFINITE);
            ambience.setVolume(lowIntensity ? 0.25 : 0.6);
            ambience.play();
        }
        if (vhsHum != null && !lowIntensity) {
            vhsHum.setCycleCount(AudioClip.INDEFINITE);
            vhsHum.setVolume(0.18);
            vhsHum.play();
        }


        // staged whisper then knock
        Timeline seq = new Timeline(
                new KeyFrame(Duration.seconds(1.2), e -> {
                    if (whisper1 != null && !lowIntensity) whisper1.play();
                    showDialogueSequence(dialogueBox, new String[]{"A distant hum. The house breathes."}, () -> {});
                }),
                new KeyFrame(Duration.seconds(3.0), e -> {
                    if (whisper2 != null && !lowIntensity) whisper2.play();
                    showDialogueSequence(dialogueBox, new String[]{"You think you hear your name called from the street."}, () -> {});
                }),
                new KeyFrame(Duration.seconds(5.0), e -> {
                    if (knock != null && !lowIntensity) knock.play();
                    showDialogueSequence(dialogueBox, new String[]{"A soft knock at the door. It sounds deliberate."}, () -> {});
                })
        );
        seq.play();
    }


    private void showDialogueSequence(TextArea box, String[] lines, Runnable onComplete) {
        box.clear();
        Timeline seq = new Timeline();
        double delay = 0;
        for (String line : lines) {
            delay += 1.0;
            String l = line;
            seq.getKeyFrames().add(new KeyFrame(Duration.seconds(delay), e -> box.appendText(l + "\n\n")));
        }
        seq.getKeyFrames().add(new KeyFrame(Duration.seconds(delay + 0.6), e -> onComplete.run()));
        seq.play();
    }


    private void handleChoiceWithTension(int epIndex, Outcome outcome, StackPane centerPane) {
        boolean highImpact = outcome.title.matches("(?i).*intruder|.*crash|.*loss|.*overdose.*");


        if (highImpact && !lowIntensity) {
            if (sting != null) sting.play();
            showJumpscareThenDebrief(epIndex, outcome, centerPane);
        } else {
            showDebrief(epIndex, outcome);
        }
    }


    private void showJumpscareThenDebrief(int epIndex, Outcome outcome, StackPane centerPane) {
        // quick white flash with slight vignette and analog flicker
        StackPane flash = new StackPane();
        flash.setStyle("-fx-background-color: rgba(255,255,255,0.95);");
        Scene flashScene = new Scene(flash, 1000, 700);
        Stage flashStage = new Stage();
        flashStage.setScene(flashScene);
        flashStage.setAlwaysOnTop(true);
        flashStage.show();


        // small analog flicker overlay on centerPane
        Timeline flicker = new Timeline(
                new KeyFrame(Duration.seconds(0.05), e -> centerPane.setOpacity(0.6)),
                new KeyFrame(Duration.seconds(0.12), e -> centerPane.setOpacity(1.0)),
                new KeyFrame(Duration.seconds(0.18), e -> centerPane.setOpacity(0.7))
        );
        flicker.setCycleCount(3);
        flicker.play();


        Timeline t = new Timeline(new KeyFrame(Duration.seconds(0.45), e -> {
            flashStage.close();
            showDebrief(epIndex, outcome);
        }));
        t.play();
    }


    private void showDebrief(int epIndex, Outcome outcome) {
        VBox root = new VBox(12);
        root.setPadding(new Insets(18));
        root.setStyle("-fx-background-color: #0f0f10;");
        Label title = new Label("Debrief — " + outcome.title);
        title.setFont(Font.font(20));
        title.setTextFill(Color.web("#f7f7f7"));
        Label text = new Label(outcome.text);
        text.setWrapText(true);
        text.setTextFill(Color.web("#dcdcdc"));


        Label tipsLabel = new Label("Practical tips and resources");
        tipsLabel.setTextFill(Color.web("#f0b429"));
        tipsLabel.setFont(Font.font(14));


        VBox tips = new VBox(6);
        tips.setPadding(new Insets(6));
        tips.setStyle("-fx-background-color: #1a1a1a; -fx-border-color: #333333; -fx-border-radius: 6; -fx-background-radius: 6;");
        switch (epIndex) {
            case 1 -> tips.getChildren().addAll(
                    new Label("- Lock doors and check before opening."),
                    new Label("- Keep phone accessible; call if unsure."),
                    new Label("- Local support: Lifeline 13 11 14.")
            );
            case 2 -> tips.getChildren().addAll(
                    new Label("- Plan sober transport before going out."),
                    new Label("- Use rideshare or a designated driver."),
                    new Label("- If an incident occurs, call emergency services.")
            );
            case 3 -> tips.getChildren().addAll(
                    new Label("- Wear protective gear and ride within limits."),
                    new Label("- Maintain your vehicle and check conditions."),
                    new Label("- First aid and emergency numbers.")
            );
            case 4 -> tips.getChildren().addAll(
                    new Label("- Verify messages via official channels."),
                    new Label("- Never enter credentials from unknown links."),
                    new Label("- Report scams to Scamwatch.")
            );
            case 5 -> tips.getChildren().addAll(
                    new Label("- Look out for friends and stay together."),
                    new Label("- Call emergency services if someone is unwell."),
                    new Label("- Have an exit plan and a sober contact.")
            );
            default -> tips.getChildren().add(new Label("- Pause and assess before acting."));
        }


        Label progress = new Label("Fragments collected: " + metaFragments);
        progress.setTextFill(Color.web("#cfcfcf"));
        Button back = new Button("Back to episodes");
        back.setOnAction(e -> {
            if (ambience != null) ambience.stop();
            if (vhsHum != null) vhsHum.stop();
            showLanding();
        });
        Button replay = new Button("Replay episode");
        replay.setOnAction(e -> showEpisode(epIndex));


        HBox nav = new HBox(8, back, replay);
        nav.setAlignment(Pos.CENTER_LEFT);


        root.getChildren().addAll(title, text, tipsLabel, tips, progress, nav);
        Scene scene = new Scene(root, 1000, 700);
        primaryStage.setScene(scene);
    }


    // Helper classes
    private static class Episode
